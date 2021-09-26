写出更好的代码
==============
:date: 2012-06-05
:category: tech
:tags: maths, book
:slug: write-better-code
:author: 陶克勤
:summary: 程序员的世界，说发展快那是对的，新的语言，新的框架，新的玩法。但是这个世界核心的东西或者技术，却不太有改变，而且将持续的产生影响

最近想看两本书<<数学之美>>和<<暗时间>>
其实这两本书的内容都有零散的看过一些,不过当可以买到纸质书的时候,还是有很想买的欲望.
再次在看暗时间的是否不幸的我点进了这个链接: http://www.joelonsoftware.com/
更不幸的是我点进了下面的一篇文章: `The Joel Test: 12 Steps to Better Code <http://www.joelonsoftware.com/articles/fog0000000043.html>`_

看了一下时间,这篇文章已经是2000年的时候写的了,在那个时候就已经用到了下面的这些技术,我只能说程序员的世界发展真的很慢,因为这些技术现在都还在用.
而且有的开发可能现在都还没有完整的run他的这些步骤.

我想来回答Joel的这些问题,在现代的一个背景下面,在我的知识范围内来看看我能想到写什么?

* Do you use source control?
	这个必须有.如果你相对比较怀旧一点,用SVN吧.
	如果你又不想依赖于中心服务器,不想看没有个commit log都要从服务器拉取,用分布式的hg 和 git.
	如果想更加的现代,可以看看bitbucket.org 和 github.com. 或许这样的合作方式,是十年前没有想到的.

* Can you make a build in one step?
	这个可以有,拿设计模式来讲我愿意把这个叫做facade.你做的越少,犯错的机会就越少.

* Do you make daily builds?
	这个应该有.我不知道在十年前用什么工具,但是现在做这个的开源工具还是不错的,比如jenkins,buildbot

* Do you have a bug database?
	这个必须有.很多私有的系统.开源的也不错: readmin, trac
	这样的工具其实很多,对于一个团队来说,果断选择一个,尽快的熟悉起来.

* Do you fix bugs before writing new code?
	这个应该有.就想GTD里面说的,如果一个事情在两分钟之内可以完成,为什么不马上就做呢?
	我觉得作为一个开发人员怎么能够忍受最近的代码里面有bug呢?

* Do you have an up-to-date schedule?
	这个对于开发来说貌似是家常便饭.开发人员真的很喜欢:"It will be done when it's done!" 
	不要问我什么时候能完成,我真的不确定?
	这篇文章还是不错的: `Evidence Based Scheduling <http://www.joelonsoftware.com/items/2007/10/26.html>`_
	如果现在让我来评估:经验+风险评估

* Do you have a spec?
	这个可以有.看过别人写的spec,感觉需要有比较高度的认识才能完成,这是我需要的技能.

* Do programmers have quiet working conditions?
	这个比较困难吧.

* Do you use the best tools money can buy?
	best的定义其实不好说,现在很多不要钱的工具用的好同样够威力.

* Do you have testers?
	这个问题不是在问我.不过我觉得tester还是必要的,毕竟需要术业有专攻.

* Do new candidates write code during their interview?
	必须.

* Do you do hallway usability testing?
	这个貌似是为GUI程序或者网页程序来的问题啊?

通过读者个感觉我必须要从理论上去解决两个问题:

1, **怎么更好的进行schecule**

2, **如何写spec.**
