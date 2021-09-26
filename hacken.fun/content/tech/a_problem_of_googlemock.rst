googlemock中遇到pthread问题
===========================
:date: 2012-04-02
:category: tech
:tags: googlemock, g++
:slug: a-compile-problem-of-googlemock
:author: Hacken
:summary: 解决在搭建googlemock环境中碰到的一个链接顺序带来的问题

今天在搭建googlemock的环境，以前都没有出过问题，今天居然碰到了，
直接checkout googlemock的代码，然后到make文件夹下面直接make，居然连接的时候连接不上，报pthread中的东西找不到。
各种check：

* 连接参数是否有 -lpthread
	有
* locate pthread
	系统中也有
* g++ -print-search-dirs
	前面找到的库也在路径中
* 直接把找到的pthread路径用-L写在makefile中
	还是报错

经过上面的步骤我觉得应该是能够解决我的问题的，什么状况，感觉超出了我的能力。。。

经过各种google，都没有找出问题来。

没办法了，各种尝试，记得最后成功的方式：把-lpthread放到了连接参数的后面，看下面的diff

.. code-block:: guess

	--- Makefile    (revision 404)
	+++ Makefile    (working copy)
	@@ -95,4 +95,4 @@
        $(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(USER_DIR)/gmock_test.cc
 
 	gmock_test : gmock_test.o gmock_main.a
	-       $(CXX) $(CPPFLAGS) $(CXXFLAGS) -lpthread $^ -o $@
	+       $(CXX) $(CPPFLAGS) $(CXXFLAGS) $^ -o $@ -lpthread


初步原因，这个取决于连接器在链接是加载文件的顺序有关系：

.. code-block:: guess

	g++ -o gmock_test gmock_test.o gmock_main.a -lpthread

上面是我可以成功编译的命令，可以看出加载的顺序是从右到左的。

从这个文档可以看到下面的解释：<http://gcc.gnu.org/onlinedocs/gcc-4.7.0/gcc/Link-Options.html#Link-Options>

.. note::

	It makes a difference where in the command you write this option; 
	the linker searches and processes libraries and object files in the order they are specified.
 	Thus, 'foo.o -lz bar.o' searches library 'z' after file foo.o but before bar.o. 
	If bar.o refers to functions in 'z', those functions may not be loaded.

不过看起来这个说明和我遇到的实际情况刚好相反，没有继续求证，但应该就是这个问题。
所以以后编译链接有问题 **链接器的加载顺序** 需要考虑
