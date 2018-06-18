常用命令
=================

shell脚本可以理解为shell命令的集合，shell命令可以分为两大类

- \ ``shell内部命令(builtin command)``\ ：在bash中内部实现的命令叫做内建命令，在文件系统上没有对应的可执行文件
- \ ``shell外部命令(binary command)``\ ：在文件系统上的某个位置(/bin、/sbin等)有一个与命令名称对应的可执行文件

关于shell脚本中可能会用到的shell命令可以参考：\ `linux工具集之shell命令 <../../../2-shellcmd/index.html>`_\ 

在此处我们主要介绍下shell脚本中使用频次最高的几个命令

- \ `read <#readll>`_\ ：获取用户输入
- \ `echo <#echoll>`_\ ：打印输出
- \ `printf <#printfll>`_\ ：打印输出
- \ `shift <#shiftll>`_\ ：剔除位置参数


.. _readll:

0x00 read
~~~~~~~~~~~~

关于\ ``read``\ 命令的用法详见：\ `IO操作之read <../../../2-shellcmd/ziooprate/index.html#readl>`_\ 

.. _echoll:

0x01 echo
~~~~~~~~~~~~

关于\ ``echo``\ 命令的用法详见：\ `IO操作之echo <../../../2-shellcmd/ziooprate/index.html#echol>`_\ 

.. _printfll:

0x02 printf
~~~~~~~~~~~~~

关于\ ``printf``\ 命令的用法详见：\ `IO操作之printf <../../../2-shellcmd/ziooprate/index.html#printfl>`_\ 

.. _shiftll:

0x03 shift
~~~~~~~~~~~

\ ``shift``\ 命令在shell脚本中用来剔除脚本的位置变量，它是shell内建命令，使用\ ``help shift``\ 命令可以查看其语法格式和使用说明，它的语法格式如下

.. code-block:: sh

	shift [n]

该命令常用来解析脚本的传入参数

	- \ ``shift``\ 表示剔除脚本的第一个传入参数，后面参数往前排
	- \ ``shift n``\ 表示剔除脚本的前n个传入参数，后面参数往前排

写一个脚本，使用形式：\ ``userinfo.sh -u username [-v {1|2}]``\ 

	- \ ``-u``\ 选项用于指定用户，而后脚本显示用户的UID和GID
	- \ ``-v``\ 选项后面是1，则显示用户的家目录路径；如果是2，则显示用户的家目录路径和shell

.. code-block:: sh

	#!/bin/bash

	[ $# -lt 2 ] && echo "less arguments" && exit 3

	if [[ "$1" == "-u" ]]; then
		userName="$2"
		shift 2       # 剔除前2个位置参数
	fi

	if [[ $# -ge 2 ]] && [ "$1" == "-v" ]; then
		verFlag=$2
	fi
	
	verFlag=${verFlag:-0}

	if [ -n $verFlag ]; then
		if ! [[ $verFlag =~ [012] ]]; then
			echo "Wrong Parameter"
			echo "Usage: `basename $0` -u UserName -v {1|2}"
			exit 4
		fi
	fi

	if [ $verFlag -eq 1 ];then
		grep "^$userName" /etc/passwd | cut -d: -f1,3,4,6
	elif [ $verFlag -eq 2 ];then
		grep "^$userName" /etc/passwd | cut -d: -f1,3,4,6,7
	else
		grep "^$userName" /etc/passwd | cut -d: -f1,3,4
	fi





