c++0x_lambda
============
:date: 2012-05-30
:category: tech
:tags: lambda, c++0x
:slug: lambda-in-c++0x
:author: Hacken
:summary: 标准的lambda用起来还是挺不错的，个人感觉比boost好很多。

看了一下,这些关于lambda的讨论,貌似都是在09年左右,不过我感觉还是很有价值的.

权威文档的话参考SPEC [1]_

从SPEC来看,lambda的语法上还是很多细节需要注意的,但是要想掌握其用法应该稍加练习就可以了.

第二个网页 [2]_ 讲一些vc10对lambda的支持,从这篇文章基本上可以看到lambda在使用上的全貌.

第三个页面 [3]_ 我比较喜欢,stackoverflow你懂的.

其实boost中已经有lambda的实现了,之前有练习过用法,没有太深入.
如果我没有记错的话boost::lambda使用上更加简洁,但限制比较多,但是功能没有std::lambda强大.
需要看看各个编译器对std::lambda的支持程度了,如果这个程度到了一个比较好的阶段,估计很多人都只会用std的了.

看看网页 [4]_ ,当我们有了auto, lambda再加上我们牛X的stl,代码写起来有木有感觉想在写脚本?
worth to try!


.. [1] SPEC: http://www.open-std.org/JTC1/SC22/WG21/docs/papers/2009/n2927.pdf
.. [2] easy sample: http://blogs.msdn.com/b/vcblog/archive/2008/10/28/lambdas-auto-and-static-assert-c-0x-features-in-vc10-part-1.aspx
.. [3] discuss: http://stackoverflow.com/questions/1203808/c0x-lambdas-coding-style
.. [4] practice lambda: http://www.gamedev.net/blog/32/entry-2254311-the-new-c-lambdas/
