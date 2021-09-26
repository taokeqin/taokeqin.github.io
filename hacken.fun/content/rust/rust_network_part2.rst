Rust 网路编程 2 echo server
############################

:date: 2015-05-06
:category: rust
:tags: rust, netwrok
:slug: rust-network-echo-server
:author: Hacken
:summary: 尝试一下用rust来实现echo server,基于rfc862

这次尝试离之前又过去了三个月了，rust已经进入了beta了：

rustc 1.0.0-beta.4 (850151a75 2015-04-30) (built 2015-05-01)

不光是网路部分，整个rust较之前都有了不小的变化。

在第一部分里面（代码在最新的rust是肯定不能用的了），我感受了一下rust的基本网络编程。但什么事情都还没有做。
这个第二部分就是来完成一个echo server.

我还是依据一个rfc来：http://tools.ietf.org/html/rfc862
这里就只实现tcp部分。
直接贴代码了，算是个笔记

.. code-block:: rust

    use std::net::{TcpListener, TcpStream};
    use std::thread;
    use std::io::{Read, Write};

    // 单个连接的处理函数
    fn handle_client(mut stream: TcpStream){
        let peer_addr = stream.peer_addr().unwrap();
        println!("connected from: {} ", peer_addr);
        let mut data = [0; 100];
        loop {
            let len = stream.read(&mut data).unwrap();
            
            if len == 0{
                println!("Peer shutdown!");
                break;
            }
            print!("{} say: ", peer_addr);
            for x in 0..len{
                print!("{}", data[x] as char);
            }
            // echo to client
            let _ = stream.write(&data[0..len]).unwrap();
        }
    }

    fn main() {
        //RFC 说echo server要运行在端口7，这里就运行在7777吧，万恶的unwrap!
        let listener = TcpListener::bind("127.0.0.1:7777").unwrap();
        for stream in listener.incoming() {
            match stream {
                Ok(stream) => {
                    //这里对每一个accept的链接，启动一个线程来处理
                    //线程函数是个colsure,不带参数
                    //捕获上下文里面的stream变量，强制move
                    thread::spawn(move || {
                        handle_client(stream);
                    });
                }
                
                Err(e) => {
                    println!("Fucked by the network problem: {} ", e);
                }
            }
        }
    }
    
很多错误没有处理，都直接unwrap了。

下一个试试写一个聊天室程序吧。用一个非常简单需求：

1，服务器启动

2，客户端连接，发送消息到服务器

3，服务器广播消息到所有连接的客户端