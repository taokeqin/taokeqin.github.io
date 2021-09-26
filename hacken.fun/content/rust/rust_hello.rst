##########################
rust hello world
##########################

:date: 2014-10-14 10:17
:category: rust
:tags: rust, hello
:slug: rust-hello
:author: Hacken
:summary: 最近rust开始逐渐走入大家的视野，我也很想尝试一下看看这个准备挑战c++的语言，是个什么样的，先来个HelloWorld.

HI
===

第一次看到rust这个语言是在一个Lang.Next的视频上，当时有一个对话场景，上面坐着4个人再回答下面的提问！

这四个人所负责的语言分别是：C++, D, rust, go

瞬间发现自己居然不知道rust这个语言呢！

我为什么想试试这个语言
======================

rust定位是系统编程语言，强类型，强调安全，强调并行, 不用GC, 不用异常

怎么安装rust
============

目前的安装方式在linux比较暴力，windows还好，直接下载安装包就可以了

linux:

.. code-block:: bash

	$ curl -s https://static.rust-lang.org/rustup.sh | sudo sh


这个安装脚本是会默认安装两个东西：rust， cargo

cargo是rust的代码依赖管理和build工具，对于习惯了make，cmake这样工具的人，还是会觉得cargo比较方便些。
后面详细分析一下cargo的使用，目前cargo还在开发阶段，特性不多，容易掌握！

官方推荐学习,练习rust，一开始就要使用cargo来做工程管理。
所以我在使用过一次rustc后，所有的代码就都用cargo了。

rustc hello world
==================

安装好了编译器之后第一步就来hello world

新建文件： main.rs

.. code-block:: rust

	fn main(){
		println!("Hello World");
	}

运行命令进行编译

.. code-block:: bash

    $rustc main.rs

可以看到生成的二进制文件，直接运行就好了。

cargo hello world
==================

cargo有直接的工程创建模板，或者可以手动按照cargo的约定进行代码布局

约定的好处就是大家干出来的结果都一样. 不像make，每个人的工程都不一样！

.. code-block:: bash

    cargo new hello_world --bin

找一个你放代码的目录，运行上面的命令，cargo就会帮你生成一个工程模板。
看看目录里面都有写什么？

src里面的源代码，写代码就写在里面！

Cargo.toml是cargo的配置文件，可以看到，这里没并没有指明有那些源文件？？

很好奇rustc的编译链接过程？后面详细分析，至少来看比c/c++简化了程序员的决策。

编译产生二进制运行文件

.. code-block:: bash

    cargo build

会生成一个target目录，可执行文件也在里面，直接运行就好了，这样build也很不错，产生的中间文件都在src的外面，代码比较整洁！

.. code-block:: bash

    cargo clean

就轻易的清理掉了build的输出，看看目录，target目录消失了！！
