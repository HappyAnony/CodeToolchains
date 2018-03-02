正则表达式
===========

参考文档

- `揭开正则表达式的神秘面纱 <http://www.regexlab.com/>`_
- `正则表达式手册 <http://jquery.cuishifeng.cn/regexp.html>`_
- `正则表达式30分钟入门教程 <http://www.jb51.net/tools/zhengze.html>`_

正则表达式，又称为规则表达式(\ ``Regular Expression``\ )，通常可以缩写成\ ``regex``\ 、\ ``regexp``\ 、\ ``regexps``\ 、\ ``regexes``\ 或\ ``RE``\   

正则表达式的发展历史

- 美国两位神经生理科学家\ ``Warren McCulloch``\ 和\ ``Walter Pitts``\ 研究出了一种用数学方式描述神经网络的方法，该方法将神经系统中的神经元描述成了小而简单的自动控制元
- 1951年，科学家\ ``Stephen Kleene``\ 在上述研究的基础上发表了\ ``《神经网事件的表示法》``\ 的论文，该论文引入了正则表达式的概念，将上述的数学控制元称为正则集合的数学符号
- Unix之父\ ``Ken Thompson``\ 将正则表达式的研究成果应用到计算搜索算法中，将此符号系统引入到编辑器\ ``QED``\ 以及\ ``grep``\ 中
- 在之后的几十年里，正则表达式的思想被广泛应用到主流操作系统工具(\ ``类Unix``\ 、\ ``Windows``\ )、主流开发语言(\ ``perl``\ 、\ ``c++``\ 、\ ``python``\ 、\ ``JavaScript``\ )等各种应用领域中


正则表达式应用场景：文本处理

- 通过模式进行文本搜索
- 通过模式进行文本替换

接下来将从以下四个方面介绍正则表达式

- \ ``语法``\ ：正则表达式的组成成分(字符和元字符)
- \ ``引擎``\ ：正则表达式的工作原理(模式匹配)
- \ ``工具``\ ：正则表达式的编写和测试工具
- \ ``应用``\ ：正则表达式的常见应用


目录
------

.. toctree::
   :titlesonly:
   :glob:

   1-syntax/index
   2-engine/index
   3-tools/index
   4-application/index