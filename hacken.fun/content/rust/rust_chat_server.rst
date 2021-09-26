#############################
rust 网路编程 3 chat server
#############################

:date: 2015-05-07 12:17
:category: rust
:tags: rust, server
:slug: rust-chat-server
:author: Hacken
:summary: 贴了一个实现的rust聊天室程序，更加深入的使用了rust的一些网络和多线程同步设施

基于：rustc 1.0.0-beta.4 (850151a75 2015-04-30) (built 2015-05-01)

这个例子没有任何设计元素在里面，就是一个简单的概念验证。
写这个程序还是发现对rust的理解还非常浅啊。

有个奇特的结论：

rust代码写起来累，和rustc搏斗，但是回顾起来觉得很自然。
比如为什么要Arc，为什么需要mut，为什么又不需要mut

另外一个新感觉rust api不太好查，左边没有索引，有些解决方案还不如直接google问题来的快，
比如下面代码里面的从vec里面删除一个元素

后面要改进一下unwrap的使用，总是unwrap满天飞。。。，
突然有点怀念exception了，怎么办？

rust里面推荐的线程间数据传递的两种方法：

1，共享状态，Arc + Mutex

2，channel

本来是想试一试channel的，就写下面的代码写累了，就暂时放下了，后面再看有时间重构不，比如起个线程推送消息


贴个代码，自己记录一下：

.. code-block:: rust

    use std::net::{TcpListener, TcpStream};
    use std::thread;
    use std::io::{Read, Write};
    use std::sync::{Arc, Mutex};

    fn handle_client(mut tcp_stream: TcpStream, clients: Arc< Mutex< Vec<TcpStream> > >){
        let peer_addr = tcp_stream.peer_addr().unwrap();
        println!("{} online!", peer_addr);
        //通知其他客户端上线了
        //用了一个scope，也可以在这个scope的结尾直接drop这个MutexGuard来解锁mutex
        {
            //lock出来一个可变的Vec，下面要push进去当前这个client
            let mut other_clients = clients.lock().unwrap();
            for mut each_client in other_clients.iter(){
                    each_client.write(
                            format!("{} is online!\n", peer_addr).as_bytes())
                        .unwrap();
            }
            other_clients.push(tcp_stream.try_clone().unwrap());
        }
        // 预分配一个u8数组来存储client发来的消息
        let mut message = [0; 1024];
        
        // 开始循环处理当前客户端的数据
        loop {
            let message_length = tcp_stream.read(&mut message).unwrap();
            if message_length == 0{
                println!("{} offline!\n", peer_addr);
                break;
            }
            print!("{} say: ", peer_addr);
            for x in 0..message_length{
                print!("{}", message[x] as char);
            }
            // 把从当前客户端读取的数据推送到所有其他的客户端
            let all_clients = clients.lock().unwrap();
            for mut each_client in all_clients.iter(){
                if each_client.peer_addr().unwrap() != tcp_stream.peer_addr().unwrap(){
                    each_client.write(
                            format!("\n{} say:\n\n    ", peer_addr).as_bytes())
                        .unwrap();
                    each_client.write(&message[0..message_length]).unwrap();
                }
            }
        }
        
        //loop跳出来到这里应该是当前这个连接已经断了，
        //从连接列表里面去掉它，
        //并且通知其他客户端下线了
        
        //这里allclients要mut，因为下面有remove操作
        let mut all_clients = clients.lock().unwrap();
        //下面这个实际上是个find操作，感觉vec的查找很弱啊，还得借助iter，不太像c++的stl
        let index = all_clients.iter().position(|c|{
                return c.peer_addr().unwrap() == peer_addr
            }).unwrap();
        all_clients.remove(index);
        for mut each_client in all_clients.iter(){
                each_client.write(format!("{} is offline!\n", peer_addr).as_bytes()).unwrap();
        }
    }

    fn main() {
        let listener = TcpListener::bind("127.0.0.1:7777").unwrap();
        //在这里定义一个全局的连接数组，方便在各个线程里面共享。
        let clients = Arc::new(Mutex::new(Vec::new()));
        for tcp_stream in listener.incoming() {
            let clients = clients.clone();
            match tcp_stream {
                Ok(tcp_stream) => {
                    thread::spawn(move || {
                        handle_client(tcp_stream, clients);
                    });
                }
                
                Err(e) => {
                    println!("Fucked by the network problem: {} ", e);
                }
            }
        }
    }
