bash faq
#############################

:title: bash faq
:date: 2015-09-08 22:20
:category: linux
:tags: bash, shell
:slug: bash-faq
:authors: Hacken
:summary: 适合我自己的一个bash faq，方便查阅，少一些google和stackoverflow阅读。
:status: draft

这个faq基于bash，其他的shell需要再google了

Q：shell里面怎么获取命令行参数？

Q: sehll里面怎么定义函数？

Q：shell里面怎么使用命令行参数或者函数参数？

Q：shell的函数怎么返回值？

Q：shell里面的exit？

Q: shell代码怎么复用？
  把需要复用的代码放到一个单独的shell文件里面，在需要调用的shell文件里面通过使用source命令进行导入。

    .. code-block:: python
    
        source util.sh
        
          
Q：shell代码怎么做单元测试？

Q：shell里面怎么mock?

Q: shell里面数组是怎么用的？

Q: shell里面怎么使用正则表达式？

Q：shell里面怎么判断字符串包含？

Q：shell里面的条件表达式怎么用？
