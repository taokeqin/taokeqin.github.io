##########################
C++ Exception
##########################
:date: 2014-09-08
:category: tech
:tags: c++, exception
:slug: cpp-exception
:author: Hacken
:Status: draft
:summary: 一时看Jon Kalb的视频看得心潮澎湃，就像自己也记录一下学到的东西，不过感觉写一个自己没有深入掌握的东西有点难度，需要一边深入理解，一边总结。

为什么我要写关于异常
=====================
以前我一直觉得在c++众多工具里面，可以不用异常这个工具，只需要用返回值这种模式就够了。

以前也见到过很多说异常危险，很不好使用的论调：

http://www.joelonsoftware.com/items/2003/10/13.html

http://250bpm.com/blog:4

如果你问我会不会使用c++的异常，我的回答是我知道有这个东西，却不知道怎么用！

我一直很喜欢下面这句话来描述我的处境，来自电影《The Pursuit Of Happyness》::

	I'm the type of person,if you ask me a question, and I don't know the answer,I'm gonna to tell you that I don't know.

	But I bet you what: I know how to find the answer, and I'll find the answer.

最近发现些事情

1. 不用异常其实很难

2. 如果开始使用异常，工具厢里面就里面多了一个重量级利器，但可能容易伤到你的脚

3. 有些很牛逼的异常使用方法

为什么不用异常很难？
====================

举一些很简单的例子，日常使用的operator new, 标准库里面的std::string

还是比较常用吧，其实我以前重来没有处理过这个类相关的异常！

就目前我说学到的，std::string会抛出三种异常：

out_of_range, length_error, bad_alloc.

没有在正确的时候catch住这些异常，程序就突然结束了！

标准库里面定义了一小撮异常：http://www.cplusplus.com/reference/exception/exception/

这些个异常都会在定义好的接口，定义好的场景抛出来！

所以，如果你用标准库，那么或多或少你都要处理异常！

为什么现在我觉得异常还是个利器?
================================

以前有个简单的想法：异常处理不就是把处理返回值的if else换成try catch么？

其实不是的，我想下面的两个资料足已解决所有的问题：

C++ FAQ: http://www.parashift.com/c++-faq-lite/exceptions.html

Jon Kalb 的三节课程： Exception-Safe Code http://exceptionsafecode.com/

我其实体会最深的一点就是在用c语言编程的时候，函数里面大段的都是错误情况的处理，而不是真正想要的主线逻辑。

虽然约定fast return可以缓解一下，但是有的人却不认同多个return分支这样的选择。

而且，这种返回值的错误完全可以不检查，恰恰有时一个没有处理到，往往就成就了一个bug。

还有的情况追代码发现这个错误代码会是一层一层的往上传递，

但是你其实无法确定中间过程有没有人处理过返回值，如果简单的认为都是透传到上层函数，shit hppens.

总的来讲，程序中错误处理是很重要也很困难的。

用C++的异常工具，我目前感觉还是可以让错误处理相对简单一点。


什么是异常安全(exception safety)
=================================

异常安全简单的来讲：

异常随时可能发生，安全就是希望在异常发生的时候，对象的状态不被破坏，获取的资源还能够正确管理不会泄露。

http://herbsutter.com/gotw/_102/

http://stackoverflow.com/questions/1853243/c-do-you-really-write-exception-safe-code

http://www.stroustrup.com/except.pdf

异常处理方法
=============

http://open-std.org/JTC1/SC22/WG21/docs/papers/2011/n3242.pdf