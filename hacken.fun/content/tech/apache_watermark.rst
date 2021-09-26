apache 用imagemagic 加水印
###########################

:date: 2012-06-05
:category: tech
:tags: apache, imagemagic, watermark
:slug: apache-add-watermark
:author: 陶克勤
:summary: 在不改变后端代码的情况下，通过配置apache ext_filter来为指定的图片加上水印

安装测试imagemagic
==================

系统基于Ubuntu 14.04, 首先安装imagemagic

.. code-block:: bash

    sudo apt-get install imagemagic

可以找点图片来测试一下imagemagic加水印的效果先。

.. code-block:: bash

    composite -gravity SouthEast watermark.png input.jpg output.jpg

gravity用来设置水印图片在输入图片的位置，有下面的值可以选：
{NorthWest, North, NorthEast, West, Center, East, SouthWest, South, SouthEast}

比如Center就会把水印图片放到原始图片中间，其他的参数试一试就知道效果了。

可以命令行参数-watermark改变水印图片的透明度.geometry是个原图的百分比
-watermark geometry  percent brightness and saturation of a watermark

.. code-block:: bash

    composite -gravity SouthEast -watermark 20 watermark.png input.jpg output.jpg

配置apache扩展
==============

如果效果已经验证了，就可以通过配置composite的处理命令给apache，让apache来自动运行这个过程。

这就需要安装模块：ext_filter，可能有多种方式进行安装，比如最原始的就是直接手动做连接，但推荐a2enmod

.. code-block:: bash

    sudo a2enmod ext_filter

模块就安装好了,增加一个配置文件,include进主配置

.. code-block:: bash

    <IfModule mod_ext_filter.c>
            ExtFilterDefine watermark mode=output intype=image/jpeg cmd="/usr/bin/composite -gravity Center watermark.png - -"
    </IfModule>

    <Location /url/to/match/image>
            SetOutputFilter watermark
    </Location>
    
重启一下服务器，这样一个简易全站的图片水印功能就出来了。

细节控制
========
经常的，不是所有的页面上的图片都需要水印，那用apache扩展的方式能不能做到很细节的控制呢？

答案是可以的。

.. code-block:: bash

    SetEnvIf Referer "xxx\.com/index" addwatermark

    <IfModule mod_ext_filter.c>
            ExtFilterDefine watermark mode=output intype=image/jpeg cmd="/usr/bin/composite -gravity SouthEast /path/to/watermark.png - -" enableenv=addwatermark
    </IfModule>

    <Location /static/images>
            SetOutputFilter watermark
    </Location>

以上的配置就是一个通过检查Referer来定义是否添加水印的例子。

大致的加水印流程就是

1. 浏览器打开页面index, 这个页面加载图片/static/images/test.jpg

2. 一个请求进来，SetEnvIf发现有对应的Referer：xxx.com/index， 设置置一个apache内部的全局环境变量addwatermark

3. 在输出图片的时候，应用输出过滤watermark

4. 由于这个过滤器有标志：enableenv=addwatermark，所以这个过滤器会生效，对输出图片做加水印动作。

else的流程也很明显，如果没有对应的Referer，在应用输出过滤的时候由于enableenv没有设置，等于过滤器是空的，撒都不做。

结论
======

由于这个水印是运行时合成，对内存和cpu的消耗应该还是很明显的。

对小流量的网站，用这种方式我觉得还是可以选择，这样可以做到不改变程序代码，而且也达到能相对细节控制的效果。

这个方式可以把水印展现给几乎所有的普通用户，一部分专业选手就没办法了，REferer这样的检查是很容易绕过的。

