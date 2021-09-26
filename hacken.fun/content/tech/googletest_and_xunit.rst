googletest和xUnit架构
=====================
:date: 2012-04-06
:category: tech
:tags: xunit, googletest
:slug: xunit-and-googletest
:author: Hacken
:summary: 整了一段时间的C++单元测试，代码越来越多，就越感觉离不开测试了，有了一点点对xUnit的想法。

对于C++的测试，现在我知道两个cppunit和googletest。虽然他们都是写写xUnit构的，但是我偏向于喜欢googletest，可能是自己用得比较熟悉的原因。

什么是xUnix架构
---------------------
我的一些简单理解，最先那个谁写了一个测试框架，然后大家都用，并且大家后来都觉得这种方式还不错，就这样成了惯例，然后在其他语言也有人根据这种模式写出类似的测试框架，不断的发展就成了xUnit框架，狭义的来讲x泛指各种语言。

xUnit有一些简单的概念：

* Testcase 测试用例
	单元测试的基本单元，用来描述一个测试点
	完整的测试由很多的测试用例构成

* Testfixture 测试固件（测试上下文）
	我觉得翻译为 **测试上下文** 比较好理解,根据这个翻译就可以看出，测试上下		文就是为了给测试用例提供一个上下文。	

* Testsuit 测试套件
	把很多测试用例放到一起，共享相同的测试上下文，这就是一测试套件

* Test execution 测试执行
	规定了测试用例的执行策略，比如为每个测试用例先建立测试上下文，然后运行测	试用例，对上下文进行清理

* Test Assertions 测试断言
	断言这个比较容易理解，即使不再测试架构里面也有用。

满足上面的所以概念，他就xUnit了，so easy！

googletest对应于xUnit
---------------------

* 测试用例 与 断言

.. code-block:: c++

	TEST(test_case_name, testname)
	{
		ASSERT_EQ(1,1);
	}

这个代码是一个 **测试用例** 的例子，在googletest中，在同一个测试用例下可以有
很多不同名字的测试,testname需要不一样。 **断言** 也出现在了上面的代码中，googletest中还有各种断言。

* 测试固件 与 测试套件

.. code-block:: c++

	class MyTest : public ::testing::Test
	{
		protected:
			MyTest(){}
			virtual ~MyTest(){}
	
			virtual void SetUp(){}
			virtual void TearDown(){}
	};
	
	TEST_F( MyTest, TEST_NAME_1 )
	{
		ASSERT_EQ(1,1);
	}
	
	TEST_F( MyTest, TEST_NAME_2 )
	{
		ASSERT_EQ(1,1);
	}

在googletest里面，需要固件就让你的testcase从::testing::Test派生出来。那测试
固件在那里呢？就在于通过这种方式，可以为你的测试用例提供一个上下文，你可以在
构造函数，SetUp函数中准备你的上下文，然后在析构函数和TearDown函数中进行必要
的清理。那测试套件又在那里呢？我的理解是逻辑上的来讲，整个就是一测试套件。

* 测试执行

那测试套件的执行来说，就是创建测试上下文，执行测试用例下面的一个测试，清理上下文。然后从新再来
