Rust 网路编程 1
##########################

:date: 2015-02-08
:category: rust
:tags: rust, netwrok
:slug: rust-network-one
:author: Hacken
:summary: 尝试一下用rust来写socket网络程序，看看能通信的rust程序是个怎样的基本结构。

Start
======
虽然现在rust的网络部分还很初级，基本都处在unstable状态。
不过简单的试试网络编程还是没问题。

最简单标准的tcp server流程：

::

    bind

    listen

    accept

这样就可以开始接受外部链接。

看看rust里面是这些步骤是该怎么做？

bind
======

通过TcpListener结构来进行bind和listen。

在impl TcpListener下面有

.. code-block:: rust

    fn bind<A: ToSocketAddr>(addr: A) -> IoResult<TcpListener>

从上面的函数的定义可以看出，bind是TcpListener的static方法，通过::调用就可以了。

为什么是static方法？没有&self参数在第一个参数位置呗，很简单。

同时上面这个接口也是个很典型的泛型接口。

参数：addr 类型 A，要求A必须要实现ToSocketAddr traits

返回值：IoResult<TcpListener>

rust里面的惯用法就是用这种result集合进行返回。
对于这个结果，经常的如果这个结果不需要进行错误处理，就直接把结果unwrap出来，出错就结束运行。

.. code-block:: rust

    //为了简单起见，引入io里面的所有东西.
    use std::io::*;

    fn main(){
         let listener = io::TcpListener::bind("127.0.0.1:9999").unwrap();
    }

这个程序是没有问题的，我们bind到一个地址，用unwrap进结果判断，如果成功，就把真正的listener绑定到左边变量。

值得注意的是bind的参数是一个字符串常量&str，文档中可以看到&str是实现了ToSocketAddr这个traits的。
开始我都觉得难以置信。

下面进行

listen
=======

.. code-block:: rust

    impl Listener<TcpStream, TcpAcceptor> for TcpListener
    fn listen(self) -> IoResult<TcpAcceptor>

从上面的接口，我们可以调用TcpListener的listen成员方法来得到一个IoResult<TcpAcceptor>
从这个接口来看，这里是一个值语义的self，如果TcpListener没有实现Copy的traits,这个地方就只能是move了。
也就是说，TcpListener在调用listen的时候，会把自己“消耗”掉，下面的代码都不能再使用了。

.. code-block:: rust

    use std::io::*;
    fn main(){
        let listener = io::TcpListener::bind("127.0.0.1:9999").unwrap();
        let acceptor = listener.listen().unwrap();
    }

当我们有了一个acceptor的时候，我们看看怎讲来accept一个链接？

accept
=======

.. code-block:: rust

    impl Acceptor<TcpStream> for TcpAcceptor
    fn accept(&mut self) -> IoResult<TcpStream>

上面的接口可以看出，可以直接调用TcpAcceptor的成员方法accept。但是注意这里的self是一个mut的引用。
所以我们要让acceptor不光是默认的只读

.. code-block:: rust

    use std::io::*;
    fn main(){
        let listener = io::TcpListener::bind("127.0.0.1:9999").unwrap();
        let mut acceptor = listener.listen().unwrap();
        let tcpstream = acceptor.accept().unwrap();
    }

有了上面的代码，我就可以accept一个链接进来了。

为了看看效果，我们看看Tcpstream的文档，打印一些链接信息出来，最简单的就是看看对端的ip，端口。

下面这个函数看起来就不错

.. code-block:: rust

    fn peer_name(&mut self) -> IoResult<SocketAddr>

其实从Tcpstream的接口来看，很多也需要可以能需要改变Tcpstream的内部状态。因为参数都要求是&mut self

.. code-block:: rust

    use std::io::*;

    fn main(){
        let listener = io::TcpListener::bind("127.0.0.1:9999").unwrap();
        let mut acceptor = listener.listen().unwrap();
        let mut tcpstream = acceptor.accept().unwrap();
        let socketaddr = tcpstream.peer_name().unwrap();
        println!("connection from: {}", socketaddr);
    }

上面的代码就是一个最基本接收一个连接的server了，然后写个client试试。
只有一行一行，当然我们只是看这个链接建立的情况，不传数据。

.. code-block:: rust

    use std::io::*;

    fn main() {
        let mut stream = TcpStream::connect("127.0.0.1:9999").unwrap();
    }

最后，我没有任何关闭socket的操作是不是有问题？