数据类型
=================

数据类型的本质：固定内存大小的别名

数据类型的作用：

- 确定对应变量分配的内存大小
- 确定对应变量所能支持的运算或操作

shell脚本是弱类型解释型的语言，在脚本运行时由解释器进行解释变量在什么时候是什么数据类型

在bash中，变量默认都是字符串类型，都是以字符串方式存储，所以在本章主要是介绍各数据类型变量所支持的运算或操作

虽说变量默认都是字符串类型，但是按照其使用场景可将数据类型分为以下几种类型：

- \ `数值型 <#valuesl>`_\ 
- \ `字符串型 <#stringsl>`_\ 
- \ `数组型 <#arraysl>`_\ 
- \ `列表型 <#listsl>`_\ 

.. _valuesl:

0x00 数值型
~~~~~~~~~~~~

首先我们来声明定义一个数值型变量：\ ``declare -i Var_Name``\ 

- 虽说声明是一个数值型变量，但是存储依然是按照字符串的形式进行存储
- 该种方式声明，变量默认是本地全局变量，可以通过\ ``local Var_Name``\ 关键字将变量修改为局部变量，可以通过\ ``export Var_Name``\ 关键字将变量导出为环境变量
- 除了使用\ ``declare -i``\ 显式声明变量数据类型为数值型，还可以像\ ``Var_Name=1``\ 由解释器动态执行隐式声明该变量数据类型为数值型

数值型变量一般支持以下运算操作

- \ `算术运算 <#arithmeticl>`_\ 
- \ `逻辑运算 <#logicl>`_\ 
- \ `数组索引 <#arrayindex>`_\ 

.. _arithmeticl:

0x0000 算术运算
~~~~~~~~~~~~~~~~~

算术运算代码示例如下

.. code-block:: sh

	#!/bin/bash

	declare -i val=5   # 显式声明数值变量
	num=2              # 隐式声明数值变量

	# 使用[]运算符执行算术表达式$val+$num
	# 使用$引用表达式执行结果
	echo "val+num=$[$val+$num]" 

	# 使用(())运算符执行算术表达式
	# 使用$引用表达式执行结果
	echo "val-num=$(($val-$num))"
	echo "val%num=$(($val%$num))"

	# 使用let关键字执行算术表达式$val*$num
	# 使用=运算符将执行结果赋值给变量
	let ret=$val*$num
	echo "var*num=$ret"

	# 使用expr命令执行算术表达式$val/$num但是$val / $num之间需要用空格隔开
	# 此时该表达式中的各个部分将作为参数传递给expr命令，最后使用``运算符引用命令的执行结果
	# 使用=运算符将命令引用结果赋值给变量
	ret=`expr $val / $num`
	echo "val/num=$ret"

	# 使用let关键字执行算术表达式+=、-=、*=、/=、%=
	let val+=$num
	echo "var+=num:$val"
	let val-=$num
	echo "var-=num:$val"
	let val*=$num
	echo "val*=num:$val"
	let val/=$sum         # 貌似let不支持/=运算符
	echo "val/=num:$val"
	let val%=$num
	echo "val%=num:$val"

	# 执行结果如下
	# val+num=7
	# val-num=3
	# val%num=1
	# var*num=10
	# val/num=2
	# var+=num:7
	# var-=num:5
	# val*=num:10
	# ./test.sh: line 19: let: val/=: syntax error: operand expected (error token is "/=")
	# val/=num:10
	# val%=num:0


由上述示例可知

- 数值类型变量支持的算术运算有：\ ``加``\ 、\ ``减``\ 、\ ``乘``\ 、\ ``除``\ 
- 数值类型变量支持的算术运算符有：\ ``+``\ 、\ ``-``\ 、\ ``*``\ 、\ ``/``\ 、\ ``%``\ 、\ ``+=``\ 、\ ``-=``\ 、\ ``*=``\ 、\ ``%=``\ 

.. _logicl:

0x0001 逻辑运算
~~~~~~~~~~~~~~~~~

.. _arrayindex:

0x0002 数组索引
~~~~~~~~~~~~~~~~


.. _stringsl:

0x01 字符串型
~~~~~~~~~~~~~~


.. _arraysl:

0x02 数组型
~~~~~~~~~~~~~~

.. _listsl:

0x03 列表型
~~~~~~~~~~~~~

