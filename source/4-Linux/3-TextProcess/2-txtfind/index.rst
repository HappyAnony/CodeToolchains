文本查找
=================

命令汇总

- \ `cut <#cutl>`_\ 
- \ `grep <#grepl>`_\ 
- \ `egrep <#egrepl>`_\ 
- \ `awk <#awkl>`_\ 
	- \ `awk选项 <#optionsl>`_\ 
	- \ `awk程序 <#programl>`_\ 
		- \ `awk匹配模式 <#patternl>`_\ 
		- \ `awk动作之变量类型 <#awkvarl>`_\ 
		- \ `awk动作之变量传递 <#awkvartl>`_\ 
		- \ `awk动作之操作符 <#awkoperal>`_\ 
		- \ `awk动作之控制流 <#awkctrl>`_\ 
		- \ `awk动作之函数 <#awkfuncl>`_\ 
	- \ `awk应用 <#awkappl>`_\ 

这些工具可以称之为文本过滤器，它们会根据用户指定的文本模式对目标文件或目标字节流进行逐行搜索匹配，并输出匹配文本模式的整行信息或特定字段

.. _cutl:

0x01 cut
~~~~~~~~~~

\ ``cut``\ 命令会根据指定的分隔符对每行文本内容进行切片，并显示出每行需要的切片

命令常用选项有：

- \ ``-d``\ ：后面紧跟字符，将该字符指定为行分隔符；默认是以\ ``tab``\ 键为分隔符
- \ ``-f``\ ：后面紧跟数字，以字段为单位显示文本每行内容；一般和\ ``-d``\ 选项指定分隔符一起使用
- \ ``-b``\ ：后面紧跟数字，以字节为单位显示文本每行内容，不需要指定分隔符
- \ ``-c``\ ：后面紧跟数字，以字符为单位显示文本每行内容，不需要指定分隔符
- \ ``--complement``\ ：显示文本每行没有被\ ``-b``\ 、\ ``-c``\ 、\ ``-f``\ 选项匹配到的内容
- \ ``-s``\ ：不显示没有被\ ``-f``\ 选项匹配到的行，和\ ``-f``\ 选项一起使用

\ ``-b``\ 、\ ``-c``\ 、\ ``-f``\ 选项后面数字都支持以下形式： 

- 一个字段、字节或字符使用一个数字即可
- 多个离散字段、字节或字符使用\ ``,``\ 分隔多个数字
- 多个连续字段、字节或字符使用\ ``-``\ 连接两个数字

    - \ ``N-``\ 表示第N个字段、字节或字符到结尾
    - \ ``-M``\ 表示第1到第M个字段、字节或字符
    - \ ``N-M``\ 表示第N到第M个字段、字节或字符




.. code-block:: sh

	$cut -f2 1.txt               # 默认以tab为分隔符
	$cut -f2 -s 1.txt            # 如果没有第2字段，则不打印该行内容
	$cut -d: -f1 /etc/passwd     # 以:分隔/etc/passwd每行内容，打印每行分隔后的第一个字段
	$cut -c1-5 /etc/passwd       # 打印每行第1到第5个字符
	$cut -b-2 /etc/passwd        # 打印每行前2个字节内容
	$cut -c1 --complement 1.txt  # 打印每行除第1个字符之外的所有字符

.. _grepl:

0x01 grep
~~~~~~~~~~~~

参考文档

- \ `grep命令中文手册 <http://www.cnblogs.com/f-ck-need-u/p/7462706.html>`_\ 

man文档关于grep命令的介绍是：\ ``global search basic regular expression（RE）and print out the line``\ ，即基本正则表达式搜索，输出整行信息。它可接收文件或字节流作为搜索对象

\ ``grep``\ 命令使用的语法格式：\ ``grep [OPTIONS] PATTERN [FILE...]``\ 

- OPTIONS常用选项

	- \ ``--color[=WHEN]``\ ：将匹配到文本模式的信息以指定的颜色显示出来。默认auto值是红色
	- \ ``-v``\ ：反向匹配，显示不能被模式匹配到的行
	- \ ``-o``\ ：仅显示被模式匹配到的字串，而非整行
	- \ ``-c``\ ：统计文件中包含文本的次数
	- \ ``-n``\ ：打印文本匹配的行号以及文件名
	- \ ``-I``\ ：只打印文件名
	- \ ``-r``\ ：多级目录中对文本递归搜索，不包括符号链接文件
	- \ ``-R``\ ：多级目录中对文本递归搜索，包括符号链接文件
	- \ ``-i``\ ：不区分大小写，ignore-case
	- \ ``-E``\ ：默认只支持基本正则表达式，如果想使用扩展正则表达式需要用-E选项
	- \ ``-A num``\ ：不仅显示匹配到的行，还显示模式下面的num行
	- \ ``-B num``\ ：不仅显示匹配到的行，还显示模式上面的num行
	- \ ``-C num``\ ：不仅显示匹配到的行，前后各显示num行
- PATTERN：查找匹配模式

	- \ `基本正则表达式BRE <../../../5-Wildcard/2-Regular/1-syntax/1-bRegEx.html>`_\ 使用\ ``-e``\ 选项，但是由于基本正则是默认支持的，所有可以省略
	- 如果想要支持\ `扩展正则表达式ERE <../../../5-Wildcard/2-Regular/1-syntax/2-eRegEx.html>`_\ ，则需要在PATTERN之前加上\ ``-E``\ 选项
	- 如果想要支持\ `Perl正则表达式PCRE <../../../5-Wildcard/2-Regular/2-engine/3-perl.html>`_\ ，则需要在PATTERN之前加上\ ``-P``\ 选项
- FILE：查找匹配对象

	- 可以是文件名
	- 也可以是管道送过来的字节流

\ **注意**\ ：\ ``PATTERN``\ 中一旦使用正则表达式的元字符(\ ``^``\ ,\ ``$``\ 等)作为文本模式，就需要用引号括起来：

- 单引号，表示强引用，不允许做变量替换
- 双引号，表示弱引用，允许做变量替换

.. code-block:: sh

	$grep -R -n "class" .               # 多级目录中对class文本进行递归搜索，打印所在文件，行号以及整行内容
	$grep -e "class" -e "vitural" file  # 匹配多个模式
	$grep -rnP "\xE4\xB8\xAD\xE6\x96\x87|\xD6\xD0\xCE\xC4" . # 使用perl正则在多级目录中递归搜索utf-8编码和gb2312编码分别是E4B8ADE69687和D6D0CEC4的中文
	$cat LOG.* | tr a-z A-Z | grep "FROM" | grep "WHERE" > b # 将日志中的所有带where条件的sql查找查找出来

.. _egrepl:

0x02 egrep
~~~~~~~~~~~~

\ ``egrep``\ 命令的使用方法和\ ``grep``\ 命令完全一样，唯一区别就是：\ ``egrep``\ 命令使用扩展正则表达式作为默认文本模式，相当于\ ``grep -E``\ 


.. _awkl:

0x03 awk
~~~~~~~~~~

参考文档

- \ `awk知识点全回顾 <http://www.cnblogs.com/f-ck-need-u/p/7509812.html#blog6>`_\

awk是一个报告生成工具(过滤显示工具)，它的工作机制是：使用指定的分隔符将读取的每一行数据进行切割，然后根据PATTERN文本模式匹配每一行内容，最后执行action动作处理匹配到的行内容

awk的分支有

- awk：早期使用
- nawk(new awk)：付费使用
- gawk(GNU awk)：linux平台使用，在linux上awk只是gawk的一个符号链接

.. figure:: ../images/1.png

\ ``awk``\ 命令的语法格式有两种：

- \ ``awk [OPTIONS] -f program-file [ -- ] FILE ...``\ 
- \ ``awk [OPTIONS] [ -- ] program FILE ...``\ 

两种语法大同小异，\ ``program-file``\ 只是文件内容为\ ``program``\ 的文件名而已。其中\ ``FILE``\ 是\ ``awk``\ 命令的操作对象，可以是以空格分隔的多个文件名，也可以是管道传送过来的字符流，甚至可以是赋值变量等；至于\ ``OPTIONS``\ 和\ ``program``\ 需要详细说明下

.. _optionsl:

0x0300 OPTIONS
^^^^^^^^^^^^^^^^

常用选项有：

- \ ``-F``\ ：指定输入行的字段分隔符，也可以通过设置内置变量\ ``FS``\ 实现，默认字段分隔符为空白符，也可通过正则表达式指定分隔符

	- \ ``-F " "``\ ：默认的，会压缩所有前导空白，包括制表符和空格
	- \ ``-F " :"``\ ：当空格后跟一个冒号时作为分隔符。会压缩前导空格，但不会匹配制表符，更不会压缩制表符
	- \ ``-F "[ ]"``\ ：只表示一个空格，不压缩任何空白
	- \ ``-F "|"``\ ：指定竖线作为分隔符
	- \ ``-F ",[ \t]*|[ \t]+"``\ ：逗号后跟0或多个空白，或者只有1或多个空白时作为分隔符

.. _programl:

0x0301 program
^^^^^^^^^^^^^^^^

\ ``program``\ 是\ ``awk``\ 的重中之重，称为awk的程序，它的格式为：\ ``'BEGIN{ACTIONS}PATTERN{ACTIONS}END{ACTIONS}'``\ ，注意此处有单引号，由此得出awk详细的执行流程是：

- \ ``awk``\ 读取\ ``program``\ 后面第一个文件第一行之前执行\ ``BEGIN``\ 后面的\ ``ACTIONS``\ 程序，该程序通常用于输出一个标题，或者初始化一些格式、变量等
- \ ``awk``\ 每读取\ ``program``\ 后面文件的一行内容就使用\ ``BEGIN``\ 或\ ``OPTIONS``\ 中定义的输入字段分隔符和输入行分隔符对行内容进行段分隔和行分隔，然后将该行内容与\ ``PATTERN``\ 文本模式进行匹配比较，如果行内容能够匹配上则执行\ ``PATTERN``\ 后面的\ ``ACTIONS``\ 程序
- \ ``awk``\ 处理完\ ``program``\ 后面最后一个文件的最后一行后执行\ ``END``\ 后面的\ ``ACTIONS``\ 程序，该程序通常用于最后的总结性输出

在\ ``program``\ 中\ 

- ``BEGIN{ACTIONS}``\ 字段不用提供输入流，\ ``BEGIN``\ 是固定字样，类似于类中的构造函数入口，\ ``ACTIONS``\ 是构造函数的函数体，其语法和\ ``PATTERN``\ 对应的\ ``ACTIONS``\ 语法一致，功能主要是初始化
- ``PATTERN{ACTIONS}``\ 是核心字段，称之为\ ``主输入循环(main input loop)``\ ，在进入主输入循环之前，可以不用提供输入流，但进入主输入循环后，必须提供输入流。

	- \ ``PATTERN``\ 称之为文本模式，类似于类中特定函数方法的调用入口，只有满足该文本模式的内容，才能调用其对应的执行函数体
	- \ ``ACTIONS``\ 称之为执行动作，类似于上述函数方法的函数体，只有满足前面的文本模式的内容，才能调用该函数体
	- 其中\ ``PATTERN``\ 或\ ``ACTIONS``\ 二者可省一：省略\ ``PATTERN``\ 时表示对所有输入流都执行\ ``ACTIONS``\ ，省略\ ``ACTIONS``\ 表示对符合条件的输入流都执行默认的\ ``print动作``\ 
- \ ``END{ACTIONS}``\ 字段不用提供输入流，\ ``END``\ 是固定字样，类似于类中的析构函数入口，\ ``ACTIONS``\ 是析构函数的函数体，其语法和\ ``PATTERN``\ 对应的\ ``ACTIONS``\ 语法一致，功能主要是收尾处理

由上述可知，我们需要关注的只有\ ``PATTERN``\ 文本模式以及\ ``BEGIN/PATTERN/END``\ 对应的\ ``ACTIONS``\ 执行动作

.. _patternl:

\ ``PATTERN``\ 模式
++++++++++++++++++++

也称为文本模式，用来过滤输入流，只有匹配文本模式的输入流才能执行\ ``PATTERN``\ 对应的\ ``ACTIONS``\ 工作，文本模式的形式有以下几种

- \ ``/regexp/``\ ：正则匹配模式，\ ``regexp``\ 为\ `正则表达式 <../../../5-Wildcard/2-Regular/index.html>`_\ ，需要使用\ ``//``\ 将其括起来，有两种匹配表达式：

	- \ ``/regexp/``\ ：表示当前行内容能被\ ``regexp``\ 匹配则为真
	- \ ``!/regexp/``\ ：表示当前行内容不能被\ ``regexp``\ 匹配就为真
	- 例如：\ ``awk -F : '/^root\>/{print $1,$7}' /etc/passwd``\ 打印/etc/passwd文件中以root开头的行
- \ ``expression``\ ：表达式匹配模式，\ ``expression``\ 是由操作符合左右数组合而成，\ ``expression``\ 不需要加斜线，且\ ``expression``\ 中操作符、左右数之间没有空格

	- 支持的操作符有：

		- 比较操作符有：

			- 数值比较：\ ``<、<=、==、!=、>=、>``\ 
			- 正则匹配：\ ``~``\ 表示能被右数\ ``/regexp/``\ 匹配模式匹配，如\ ``$7~/bash$/``\ 
			- 正则反匹配：\ ``!~``\ 表示不能被右数\ ``/regexp/``\ 匹配模式匹配，如\ ``$7!~/bash$/``\ 
		- 算术操作符有：\ ``+、-、*、/、%、^(取幂)、**(取幂，非POSIX标准，不可移植)``\  
		- 逻辑操作符有：\ ``&&、||、!``\ ，如\ ``$4 == "Asia" && $3 > 500``\ ，\ ``! (NR > 1 && NF > 3)``\ 
	- 操作符左数可以是

		- 使用\ `字段变量 <#siteargl>`_\ ：\ ``$0``\ 表示整行、\ ``$1~$n``\ 分别表示被输入字段分隔符分隔号的\ ``第1字段``\ 和\ ``第n字段``\ 
		- 使用\ `内置变量 <#builtinl>`_\ 
	- 操作符右数可以是

		- 如果左数是数值，则操作符一般是数值比较，右数一般也是数值
		- 如果左数是字符串，则操作符一般是正则匹配，右数一般是\ ``/regexp/``\ 
	- 例如：\ ``awk -F : '$7~/bash$/{print $1}' /etc/passwd``\ 打印/etc/passwd文件中第7段是以bash结尾的行的第1段
- 地址定界模式，该模式有两种形式

	- \ ``/regexp1/,/regexp2/``\ ：第一次被\ ``regexp1``\ 匹配到的行开始到第一次被\ ``regexp2``\ 匹配到的行结束，这些内容都可以执行\ ``PATTERN``\ 后面的\ ``ACTIONS``\ 动作
	- \ ``expression1,expression2``\ ：第一次满足\ ``expression1``\ 的行开始到第一次满足\ ``expression2``\ 的行结束，这些内容都可以执行\ ``PATTERN``\ 后面的\ ``ACTIONS``\ 动作

需要注意的是：\ ``regexp``\ 正则表达式可以被赋值给一个变量，然后引用该变量来匹配数据

.. code-block:: sh

	reg="^[0-9]+$"
	$2~reg

.. _awkvarl: 

\ ``awk``\ 不需声明变量数据类型，它内置字符串类型和数值类型

\ ``ACTIONS``\ 支持的变量有以下几类
+++++++++++++++++++++++++++++++++++++++++++

- 普通变量：也可以称为自定义变量

	- 如果要赋值字符串给自定义变量，则应该使用双引号将其括起来：\ ``reg="^[0-9]+$"``\ 

		- \ ``name = "abc" "bcd"``\ 等价于\ ``name="abcbcd"``\ ,可以将空格理解为awk的拼接字符，因为awk会忽略任何不被引号包围的空白
	- 如果要赋值数值给自定义变量，则不需要使用双引号：\ ``reg=9``\ 

.. _siteargl:

- 字段变量：每行内容被输入字段分隔符分隔形成的变量

	- \ ``$0``\ ：表示整行内容
	- \ ``$1~$n``\ ：表示每行使用分隔符分隔后的第一字段~第n字典

.. _builtinl:

- 内置变量：该变量可分为两类

	- awk内部自动修改的变量

		- \ ``ARGV``\ ：命令行参数数组，从0开始计数直到ARGC-1
		- \ ``ARGC``\ ：ARGV数组元素的个数
		- \ ``FILENAME``\ ：当前处理的文件名
		- \ ``FNR``\ ：当前处理文件的记录号(行号)(file record num)
		- \ ``NR``\ ：已处理的总记录数(总行数)，多个文件时不重置(record num)
		- \ ``NF``\ ：当前行使用分隔符分隔完后的字段总数(field num) 
	- awk内部不会改动的系统变量，完全需要手动修改，这类一般都有默认值

		- \ ``FS``\ ：输入字段分隔符，默认为空白(field separate)
		- \ ``OFS``\ ：输出字段分隔符，默认为空白(output field separate)
		- \ ``RS``\ ：输入流记录(行)分隔符，默认为\ ``\n``\ ，该变量只取变量值的第一个字符(record separate)，若设置为\ ``\t\t``\ ，则第二个\ ``\t``\ 被忽略
		- \ ``ORS``\ ：输出流记录(行)分隔符，默认为\ ``\n``\ ，该变量可识别多字符(output record separate)
		- \ ``OFMT``\ ：printf输出数值转换成字符串输出时的格式，默认为\ ``%.6g``\ 
		- \ ``CONVFMT``\ ：printf输出数值转换成字符串输出时的格式，会被OFMT覆盖，默认为\ ``%.6g``\ 
		- \ ``RLENGTH``\ ：被match函数匹配的字符串的长度
		- \ ``RSTART``\ ：被match函数匹配的字符串的开始位置
		- \ ``SUBSEP``\ ：下标分隔符，默认为\ ``\034``\ ，ASCII中\ ``034``\ 代表的是双引号\ ``"``\ 
- 数组变量：\ ``awk数组``\ 和\ ``shell数组``\ 类似，都支持\ ``数值index``\ 的普通数组和\ ``字符串index``\ 的关联数组，其实\ ``数值index``\ 仍然会转换成\ ``字符串index``\ ，所以awk的数组类型都是关联数组

	- 数组格式：\ ``array_name[index]``\ 
	- 数组访问

		- 获取数组元素

			- \ ``array_name["var"]``\ ：index为var的数组元素，若该数组元素没有定义，则会定义一个新的数组元素\ ``array_name[""]``\ 
			- \ ``array_name[var]``\ ：index为变量var的值的数组元素，若该数组元素没有定义，则会定义一个新的数组元素\ ``array_name[""]``\ 
		- 判断数组元素是否存在

			- \ ``if ("var" in array_name)``\ ：判断数组\ ``array_name``\ 中是否有\ ``var下标``\ 对应的数组元素。如果有，它会返回1，否则返回0
			- \ ``if (array_name["var"] != "")``\ ：判断数组变量的值是否为空也可判断该数组元素是否存在，但当该元素不存在时，会创建它，一般不采用这种方式来判定
		- 循环遍历数组：\ ``for (i in array_name){do something about array_name[i]}``\ 

			- 变量\ ``i``\ 用来遍历数组的\ ``index``\ ，\ ``array_name``\ 是数组名
			- 这种方法是以遍历index的方式来遍历数组。由于index的顺序随机，所以遍历时顺序也是随机的
		- 删除数组元素或数组

			- \ ``delete array_name["var"]``\ ：删除\ ``array_name``\ 中下标为\ ``var``\ 的元素
			- \ ``delete array_name``\ ：删除数组\ ``array_name``\ 

.. _awkvartl:

\ ``ACTIONS``\ 接收变量的途径有
++++++++++++++++++++++++++++++++

此处所说的变量主要是指外界变量，例如：\ ``shell中的变量``\ 、\ ``shell中命令执行的结果``\ 、\ ``开始执行awk前应该初始化的变量``\ 等

1. \ **将待传递变量当作文件名被awk解析**\ 

变量赋值语句定义位置：\ ``program``\ 之后

该种定义方式的特点是：变量不可在\ ``BEGIN``\ 中使用，因为它是被当做文件解析的，只有在需要读取主输入文件的时候才会被解析，也就是说执行完\ ``BEGIN``\ 后才会来解析赋值变量

.. code-block:: sh

	awk 'BEGIN{}PATTERN{print var1,var2,var3}' var1=value1 var2=value2 file1 var3=value3 var1=value4 file2

在上面的语句中

- 当awk执行完BEGIN程序后，准备读取主输入，于是开始解析program后的输入文件
- 解析时发现，var1和var2都是赋值语句，于是当成变量处理
- 当读取到file1时，发现只有一个参数，则当作输入文件，于是开始处理该文件。在处理file1时，var1和var2都是有效的，但var3还未赋值，因此var3无效
- 当处理完file1后，继续解析下一个主输入文件，此时var3被赋值，并开始处理file2
- 在处理file2时，var1、var2和var3都是有效的，但var1被新值覆盖

此外，还可以将shell命令的结果赋值给这些预定义变量。如下展示了几种变量定义的方式：

.. code-block:: sh

	name="Ma longshuai"
	awk 'program' OFS=":" var1="$name" var2="`echo Ma longshuai2`" var3="Ma longshuai3" var4=Malongshuai4 filename

2. \ **使用-v选项传递变量**\ 

变量赋值语句定义位置：\ ``program``\ 之前

该种定义方式的特点是：变量可以在\ ``BEGIN``\ 中使用，因为它定义在\ ``program``\ 之前，先解析赋值变量，然后执行\ ``BEGIN``\ 

每定义一个变量，都需要使用一个\ ``-v``\ 选项，例如：

.. code-block:: sh

	name="Ma longshuai"
	awk -v OFS=":" -v var1="$name" -v var2="`echo Ma longshuai2`" -v var3="Ma longshuai3" 'program' filename

3. \ **通过参数数组ARGV传递变量**\ 

\ ``ARGV``\ 是内置的数组变量。awk内部会将\ **命令行**\ 切分，并按规则将各参数存放到ARGV数组中，数组下标从0开始，这是awk中唯一下标从0开始的数组。在存放到``\ ARGV``\ 时，所有的选项和program会被忽略。

每存储一个数组变量，特殊变量\ ``ARGC``\ 的值增加1。因此\ ``ARGC``\ 的值代表的是参数的个数。所以，数组变量从\ ``ARGV[0]``\ 到\ ``ARGV[ARGC-1]``\ 

可使用类似下面的循环来遍历ARGV数组

.. code-block:: sh

	awk -F "\t" -v var1="value1" 'BEGIN{
		for(i=0;i<ARGC;++i){
			print "ARGV[" i "]: " ARGV[i]
		}
		print "ARGC: " ARGC
	}' "a" "b" "v=1" file

	# 输出结果
	ARGV[0]: awk
	ARGV[1]: a
	ARGV[2]: b
	ARGV[3]: v=1
	ARGV[4]: file
	ARGC: 5

注意，\ ``ARGV[0]``\ 存储的是awk命令，\ ``-F``\ 和\ ``-v``\ 选项都没有存储到ARGV中

\ ``ARGC``\ 和\ ``ARGV``\ 数组变量的值都可以手动修改。命令行分割存储完成之后，开始处理\ ``BEGIN``\ ，再处理主循环输入。因此，在\ ``BEGIN``\ 里修改\ ``ARGV``\ 中输入文件对应的值，可以改变\ ``awk``\ 所读取的输入文件，若将其设置为空，则该数组变量直接被跳过，也就不再读取该输入文件

需要注意的是，当增加\ ``ARGV``\ 元素时，必须同时递增\ ``ARGC``\ 的值，因为awk是根据\ ``AGRC``\ 来读取\ ``ARGV``\ 的。同理，只增加\ ``ARGC``\ 的值，将导致新建\ ``ARGV``\ 数组元素，且这些新元素的值为空。也因此，如果减小\ ``ARGC``\ 的值，将导致无法访问超出\ ``ARGC-1``\ 边界的\ ``ARGV``\ 元素。

.. _awkoperal:

\ ``awk``\ 的\ ``ACTIONS``\ 中支持的运算符有
++++++++++++++++++++++++++++++++++++++++++++++

- 比较操作符：

	- 数值比较：\ ``<、<=、==、!=、>=、>``\ 
	- 正则匹配：\ ``~``\ 表示能被右数\ ``/regexp/``\ 匹配模式匹配，如\ ``$7~/bash$/``\ 
	- 正则反匹配：\ ``!~``\ 表示不能被右数\ ``/regexp/``\ 匹配模式匹配，如\ ``$7!~/bash$/``\ 
- 算术操作符：\ ``+、-、*、/、%、^(取幂)、**(取幂，非POSIX标准，不可移植)``\  
- 赋值操作符：\ ``++、--、+=、-=、*=、/=、%=、^=、**=``\ awk支持复合赋值，例如\ ``FS = OFS = "\t"``\ 表示输入字段分隔符和输出字段分隔符都被赋值为制表符
- 逻辑操作符：\ ``&&、||、!``\ ，如\ ``$4 == "Asia" && $3 > 500``\ ，\ ``! (NR > 1 && NF > 3)``\ 

.. _awkctrl:

\ ``awk``\ 的\ ``ACTIONS``\ 中支持的控制流语句有
+++++++++++++++++++++++++++++++++++++++++++++++++

- 赋值语句
- 条件判断语句
- 循环语句

此处的赋值语句主要指的是对变量的赋值操作，不同赋值语句之间使用\ ``;``\ 隔开

- \ ``FS="\n";RS=""``\ 
- \ ``name="test anony"``\ 

条件判断语句的格式有

.. code-block:: sh

	# if多行格式
	if(测试表达式){
		cmd1
		cmd2
		...
	}

	# if单行格式
	if(测试表达式){cmd1;cmd2;...}

	# if-else单行格式
	if(测试表达式){cmd1;cmd2;...}else{cmd3;cmd4;...}

	# if-else多行格式
	if(测试表达式){
		cmd1
		cmd2
		...
	}else{
		cmd3
		cmd4
		...
	}

	# if-elseif-else单行格式
	if(测试表达式){cmd1;cmd2;...}else if{cmd3;...}else{cmd7;...}

	# if-elseif-else多行格式
	if(测试表达式){
		cmd1
		cmd2
		...
	}
	else if{
		cmd3
		cmd4
		...
	}
	else{
		cmd5
		cmd6
		...
	}

	# 三目运算符
	test_cmd ? cmd1 : cmd2

循环语句的格式有

.. code-block:: sh

	# while循环多行格式
	while(测试表达式){
		cmd1
		cmd2
		....
	}

	# while循环单行格式
	while(测试表达式){cmd1;cmd2;....}

	# do循环多行格式
	do{
		cmd1
		cmd2
	}while(测试表达式)

	# do循环单行格式
	do{cmd1;cmd2}while(测试表达式)

	# for循环多行格式
	for(变量初始值; 测试表达式; 计数器增长表达式){
		cmd1
		cmd2
		....
	}

	for(变量 in 数组){
		cmd1
		cmd2
		....
	}

	# for循环单行格式
	for(变量初始值; 测试表达式; 计数器增长表达式){cmd1;cmd2;....}

	for(变量 in 数组){cmd1;cmd2;....}

需要说明的是：以上格式中所有\ ``cmd``\ 的本质就是一个\ `语句 <#awkctrl>`_\ 或一个\ `函数 <#awkfuncl>`_\ 

在循环语句中有几个可以影响循环的动作

- \ ``break``\ ：退出循环。
- \ ``continue``\ ：退出当前循环，进入下一个循环
- \ ``next``\ ：读入下一行，awk程序的顶端从头开始，该语句只适用于\ ``PATTERN{action}``\ 这部分，不适用\ ``BEGIN{action}``\ 
- \ ``exit code``\ ：直接进入END，若本就在END中，则直接退出awk；如果END中的exit没有定义code，则采用前一个exit的code。

.. _awkfuncl:

\ ``awk``\ 的\ ``ACTIONS``\ 中支持的函数有
+++++++++++++++++++++++++++++++++++++++++++

- \ `输出函数 <#outfuncl>`_\ 
- \ `算术函数 <#algfuncl>`_\
- \ `字符串函数 <#strfuncl>`_\ 
- \ `自定义函数 <#deffuncl>`_\ 
- \ `system函数 <#sysfuncl>`_\ 
- \ `getline函数 <#getfuncl>`_\ 

.. _outfuncl:

\ ``ACTIONS``\ 中使用\ ``print``\ 和\ ``printf``\ 函数输出数据，不仅可以输出到标准输出中，还可以重定向到文件中，甚至可以使用管道传递给另一个命令

.. code-block:: sh

	# 输出数据到标准输出上
	print                           # 将$0打印到标准输出,等价于print $0
	print expression expression ... # 将各个expression的内容进行拼接然后打印到标准输出上，由ORS终止
	print expression,expression,... # 打印各个expression, expression之间由OFS 分开, 由ORS终止
	printf(format,expression,expression,…) # 格式化输出到标准输出上

	# 输出数据重定向到文件
	# 以下文件名filename必须使用双引号包围，否则被当作变量，且文件只会被打开一次
	print expression,expression,… > filename  # 覆盖原文件内容
	print expression,expression,… >> filename # 追加到原文件中
	printf(format,expression,expression,…) > filename  # 格式化覆盖原文件内容
	printf(format,expression,expression,…) >> filename # 格式化追加到原文件中

	# 输出数据到另一个命令
	# 以下命令需要使用双引号包围
	print expression,expression,… | command  # 将数据传递给系统命令
	printf(format,expression,expression,…) | command # 将格式化数据传递给系统命令

注意：如果print或printf的参数列表中含有操作符，则需要使用括号包围，否则容易产生歧义

.. code-block:: sh

	print($1, $3) > ($3 > 100 ? "bigpop" : "smallpop")
	print $1, ($2 > $3)

这里需要说明的是\ ``format格式化字串``\ ，它是一个包含输出格式说明符的纯文本字符串，输出格式说明符使用\ ``%``\ 来描述，然后跟着几个字符，这些字符控制一个value的输出格式。第一个\ ``%``\ 描述value1的输出格式，第二个\ ``%``\ 描述value2的输出格式，依次类推。因此，\ ``%``\ 的数量应该和被输出的value数量一样多

格式说明符\ ``%``\ 后可跟的字符有

- 格式符

	- \ ``%d``\ ：以十进制整数显示
	- \ ``%i``\ ：以十进制整数显示
	- \ ``%f``\ ：以浮点数显示
	- \ ``%s``\ ：以字符串显示
	- \ ``%u``\ ：以无符号整数显示
	- \ ``%%``\ ：显示%自身
	- \ ``%.``\ ：以小数值格式输出
- 修饰符

	- \ ``N``\ ：显示宽度；N为数值，宽度不足时若为左对齐则右边空格补足，若右对齐则左边空格补足
	- \ ``-``\ ：左对齐
	- \ ``+``\ ：显示数值正负号
	- \ ``0``\ ：表示以0填充


例如：

.. code-block:: sh

	printf("total pay for %s is $%.2f\n", $1, $2 * $3) 
	# 按字符串格式输出"$1"，按小数值格式输出"$2 * $3"，且小数位占2位

	printf("%-8s $%6.2f\n", $1, $2 * $3)               
	# "%-8s"表示"$1"按字符串格式输出，但短横线"-"表示要左对齐输出，"8"表示占用8个字符宽度，不足之数在右边空格补齐
	# "%6.2f"表示按小数格式输出"$2 * $3"，且小数位占用2位，总字符数占用6位。小数点也占用一个字符宽度。因此，一个可能的输出值为"123.20"

.. _algfuncl:

\ ``ACTIONS``\ 中支持的算术函数有

- \ ``cos(x)``\ ：取x的余弦
- \ ``sin(x)``\ ：取x的正弦
- \ ``sqrt(x)``\ ：取x的平方根
- \ ``rand()``\ ：返回一个随机数r，范围是[0,1)
- \ ``srand(x)``\ ：设置rand()的种子值为x。种子值相同时，rand()的结果相同。可print srand()输出当前种子值
- \ ``int(x)``\ ：取x的整数部分

.. _strfuncl:

\ ``ACTIONS``\ 中支持的字符串函数有(建议下面的所有regexp都使用\ ``//``\ 包围)

- \ ``index(str1,str2)``\ ：返回子串str2在字符串str1中第一次出现的位置。如果没有指定str1，则返回0
- \ ``length(str1)``\ ：返回字符串str1的长度。如果未给定str1，则表示计算"$0"的长度
- \ ``substr(str1,p)``\ ：返回str1中从p位置开始的后缀字符串
- \ ``substr(str1,p,n)``\ ：返回str1中从p位置开始，长度为n的子串
- \ ``match(str1,regexp)``\ ：如果regexp能匹配str1，则返回匹配起始位置。否则返回0。它会设置内置变量RSTART和RLENGTH的值
- \ ``split(str1,array,sep)``\ ：使用字段分隔符sep将str1分割到数组array中，并返回数组的元素个数。如果未指定sep则采用FS的值。因此该函数用于切分字段到数组中，下标从1开始
- \ ``sprintf(fmt,expr)``\ ：根据printf的格式fmt，返回格式化后的expr
- \ ``sub(regexp,rep,str2)``\ ：将str2中第一个被regexp匹配的字符串替换成rep，替换成功则返回1(表示替换了1次)，否则返回0，注意是贪婪匹配，替换字符串rep中使用\ ``&``\ 符号表示反向引用，引用的是整个被匹配的部分
- \ ``sub(regexp,rep)``\ ：将"$0"中第一个被regexp匹配的字符串替换成rep，替换成功则返回1，否则返回0，注意是贪婪匹配，替换字符串rep中使用\ ``&``\ 符号表示反向引用，引用的是整个被匹配的部分
- \ ``gsub(regexp,rep,str2)``\ ：将str2中所有被regexp匹配的内容替换成rep，并返回替换的次数，替换字符串rep中使用\ ``&``\ 符号表示反向引用，引用的是整个被匹配的部分
- \ ``gsub(regexp,rep)``\ ：将"$0"中所有被regexp匹配的内容替换成rep，并返回替换的次数，替换字符串rep中使用\ ``&``\ 符号表示反向引用，引用的是整个被匹配的部分
- \ ``toupper(str)``\ ：将str转换成大写字母，并返回新串
- \ ``tolower(str)``\ ：将str转换成小写字母，并返回新串

.. code-block:: sh

	awk 'BEGIN{
		print index("banana","na")
		print length("banana")
		print match("banana","na.*")
		print toupper("banana")
		print substr("banana",3)}'
	# 输出结果
	#3
	#6
	#3
	#BANANA
	#nana

	awk 'BEGIN{str1="x&x";str2="banana"
		print sub(/a.*n/,str1,str2)
		print str2}'
	# 输出结果
	#1
	#bxananxa
	
	awk 'BEGIN{
		print match("banana",/a.*n/)
		print RSTART,RLENGTH}'
	# 输出结果
	#2
	#2 4

	awk 'BEGIN{print sprintf("hello %i world %5s","123","abc")}'
	# 输出结果
	#hello 123 world   abc

	awk 'BEGIN{
		name="Ma long shuai"
		split(name,myname)
		for (i in myname){
			print myname[i]}
		}'
	# 输出结果
	#Ma
	#long
	#shuai

	awk 'BEGIN{
		name="Ma:long:shuai"
		if (match(name,/:[^:]*:/)){
			print substr(name,RSTART+1,RLENGTH-2)}}'
	# 输出结果(将匹配成功的字符串输出出来)
	#long

.. _deffuncl:

自定义函数的格式如下

.. code-block:: sh

	function name(parameter-list) {
		statements
	}

自定义函数有以下特点

- 函数中的变量不影响函数外的变量，但可以使用外部变量。参数列表使用逗号分隔，这些参数只在函数内部生效
- 函数的定义可以在awk的引号内任意位置(即使是BEGIN之前或END之后)，但不能定义在\ ``BEGIN、主输入循环、END内部``\ ，否则自定义函数的大括号会和包围action的大括号冲突而报错
- 函数的调用位置可以在函数的定义位置之前
- 在函数的\ ``statements``\ 中，可以使用\ ``return expression``\ 语句，表示函数的返回值

如下\ **(1)-(4)**\ 处位置可定义函数，可在任意位置处调用函数

.. code-block:: sh

	awk '(1)BEGIN{ACTIONS}(2)PATTERN{ACTIONS}(3)END{ACTIONS}(4)'

以下实例创建了一个\ **向字符串指定位置处插入一个字符**\ 的函数

.. code-block:: sh

	awk 'function insert(STRING, POS, INS) {
		before_tmp = substr(STRING, 1, POS)
		after_tmp = substr(STRING, POS + 1)
		return before_tmp INS after_tmp
	}
	BEGIN{print insert("banana",3,"x")}'


.. _sysfuncl:

\ ``system``\ 函数可以用来执行系统命令，但是命令需要使用引号包围，函数的返回值是命令的退出状态

.. code-block:: sh

	awk 'BEGIN{system("fdisk -l")}'
	awk 'BEGIN{name="ma long shuai";system("echo " name)}'

.. _getfuncl:

\ ``getline``\ 函数主要功能是：从文件、标准输入或管道中读取数据，并按情况设置变量的值。它可以自动不断的加载下一行

关于该函数的返回值有如下情况：

- 如果能读取记录，则返回值为1
- 如果遇到输入流的尾部，则返回值为0
- 如果不能读取记录(如文件没有读取权限、文件不存在)时，则返回值为-1

该函数有以下使用格式

- \ ``getline``\ ：会从主输入文件中读取记录，会同时设置\ ``$0,NF,NR,FNR``\ 
- \ ``getline var``\ ：会从主输入文件中读取记录，并将读取的记录赋值给变量var，同时会设置\ ``var,NR,FNR``\ 
- \ ``getline < file``\ ：从外部文件file中读取记录，同时会设置\ ``$0,NF``\ ，需要使用双引号包围文件名，否则被当成awk中的变量
- \ ``getline var < file``\ ：从外部文件file中读取记录，并将读取的记录赋值给变量var，需要使用双引号包围文件名，否则被当成awk中的变量
- \ ``cmd | getline``\ ：从管道中读取记录，会同时设置\ ``$0,NF``\ 
- \ ``cmd | getline var``\ ：从管道中读取记录，并将读取的记录赋值给变量var

也就是说

- 当getline从非主输入文件读取记录时，不会设置NR和FNR
- 当getline后没有给定变量var时，会将读取的记录赋值给$0，于是会同时设置NF并切分成字段；否则将读取的记录赋值给变量var，不会设置NF切分字段

例如

.. code-block:: sh

	# 执行Linux下的who命令并传递给getline读取，每读取一行记录，变量n自增1
	while ("who" | getline){n++}

	# 将Linux命令date的结果保存到awk的变量date中
	"date" | getline date

	# 写成循环时，当无法读取file，返回值为"-1"，而while循环的判断条件是0和非0，所以"-1"会进入死循环
	while (getline <"file" >0){cmd...}

.. _awkappl:

0x0302 应用实例
^^^^^^^^^^^^^^^^

.. code-block:: sh

	awk 'BEGIN{name="ma long shuai";print (1,2,3,4) | "echo " name}'
	awk 'BEGIN{while (("fdisk -l" | getline) >0){print $0}}'
	awk 'BEGIN{system("fdisk -l")}'
	awk 'BEGIN{name="ma long shuai";system("echo " name)}'
	awk -F':' '$7 == "/bin/bash"{print "who use bash shell: ",$1}' /etc/passwd
	awk 'BEGIN{print "ID NAME GENDER GENDER";print ""}{print $0}END{print "total num: " NR}'
	awk 'BEGIN{print rand();print rand();srand();print rand();print rand();print srand()}'




