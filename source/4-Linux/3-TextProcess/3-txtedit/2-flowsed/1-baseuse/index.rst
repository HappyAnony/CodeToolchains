sed基本使用
=================

本文目录

- \ `sed本质 <#naturel>`_\ 
- \ `sed语法 <#syntaxl>`_\ 
- \ `sed实现机制 <#mechanisml>`_\ 
- \ `sed常用选项 <#awkoptionsl>`_\ 
- \ `sed脚本 <#awkprograml>`_\ 

	- \ `sed脚本之范围定界 <#scopel>`_\ 
	- \ `sed脚本之操作命令 <#opcmdl>`_\ 
- \ `sed输入流 <#sedinputl>`_\ 
- \ `sed应用 <#applyl>`_\

.. _naturel:

0x00 sed本质
--------------

sed的本质就是一个流式编辑器。流式编辑器用于对输入流(文件、管道、标准输入传递的数据)执行基本的文本转换操作。它有以下特点(区别于其它类型编辑器)：

- 能够筛选过滤管道传递过来的文本数据
- 只能通过一次输入流，即每次的输入流只能处理一次，因此它的效率更高。例如：\ ``(sed -n '2{p;q}';sed -n 3{p;q}) < filename``\ 命令中，第二个sed语句读取的输入流是空流

.. _syntaxl:

0x01 sed语法
--------------

sed命令的语法格式是：\ ``sed OPTIONS... [SCRIPT] [INPUTFILE...]``\ 

- \ ``OPTIONS``\ ：\ `sed命令常用选项 <#awkoptionsl>`_\ ，注意当使用\ ``-e``\ 或\ ``-f script_file``\ 选项指定SCRIPT时，\ ``[SCRIPT]``\ 参数将会被屏蔽，即sed将会将其当做输入文件处理
- \ ``[SCRIPT]``\ ：\ `sed命令脚本 <#awkprograml>`_\ ，\ ``[SCRIPT]``\ 是第一个非选项参数，sed仅在没有使用\ ``-e``\ 或\ ``-f script_file``\ 选项指定SCRIPT时，才将其当作是\ ``script部分而非输入文件``\ 
- \ ``[INPUTFILE...]``\ ：\ `sed命令输入流 <#sedinputl>`_\ ，\ ``[INPUTFILE...]``\ 是第二个非选项参数，如果不指定\ ``INPUTFILE``\ 或者指定的\ ``INPUTFILE``\ 为\ ``-``\ ，sed将从标准输入中读取输入流并进行过滤


将上述\ ``sed语法格式``\ 可以扩写成以下几种语法(本质是对\ ``SCRIPT``\ 部分的展开)

.. code-block:: sh

	# 没有使用-e选项的单个表达式==单行
	sed OPTIONS... Address{cmd1;cmd2;cmd3...} [INPUTFILE...] # OPTIONS没有-e/f选项，[SCRIPT]选项是Address{cmd1;cmd2;cmd3...}


	# 使用-e选项的单个表达式==单行
	sed OPTIONS... -e 'Address{cmd1;cmd2;cmd3...}' [INPUTFILE...] # 使用了OPTIONS中的-e选项指定SCRIPT，此时[SCRIPT]选项被屏蔽省略


	# 没有使用-e选项的多个表达式==单行，分号分隔
	sed OPTIONS... Address1{cmd1;cmd2;cmd3};Address2{cmd1;cmd2;cmd3}... [INPUTFILE...]


	# 使用-e选项的多个表达式==单行，空格分隔
	sed OPTIONS... -e 'Address1{cmd1;cmd2;cmd3}' -e 'Address2{cmd1;cmd2;cmd3}' ... [INPUTFILE...]


	# 多个表达式==分行
	sed OPTIONS... Address1{
		cmd1
		cmd2
		cmd3
	}
	Address2{
		cmd1
		cmd2
		cmd3
	}
	[INPUTFILE...]


	# 使用-f选项指定SCRIPT脚本文件
	sed OPTIONS... -f test.sed [INPUTFILE...] # 使用了OPTIONS中的-f选项指定SCRIPT脚本文件，test.sed脚本文件的内容可以如下所示
	#!/usr/bin/sed -f
	#注释行
	Address1{cmd1;cmd2...}
	Address2{cmd1;cmd2...}
	......

.. _mechanisml:

0x02 sed实现机制
------------------

\ ``sed``\ 实现机制可以使用下列编程结构来描述

.. code-block:: sh

	for (line=1;line<=last_line_num;++line)
	do
		read $line to pattern_space;
		while pattern_space is not null
		do
			if Address1;then execute cmd1 in SCRIPT;
			if Address2;then execute cmd2 in SCRIPT;
			if Address3;then execute cmd3 in SCRIPT;
			……
			auto_print;
			remove_pattern_space;
		done
	done

\ ``sed``\ 在对输入流进行处理时，维护了两个数据缓冲空间(这两个空间初始时都为空)

- 一直处于活动状态的\ ``模式空间(pattern space)``\ 
- 辅助性的\ ``保持空间(hold space)``\ 

实现sed机制的编程结构中包含两个循环

- \ `sed循环 <#sedll>`_\ ：外层for循环
- \ `SCRIPT循环 <#scriptll>`_\ ：内层while循环

.. _sedll:

\ ``sed循环``\ 的处理流程是

- \ **(1)**\ sed读取输入流(文件、管道、标准输入)中的一行，移除该行的尾随换行符，并将其放入到\ ``模式空间(pattern space)``\ 中，同时将此行行号通过sed行号计数器记录在内存中
- \ **(2)**\ 执行\ `SCRIPT循环 <#scriptll>`_\ 处理\ ``模式空间(pattern space)``\ 中读入的行内容
- \ **(3)**\ 跳出\ `SCRIPT循环 <#scriptll>`_\ 后返回第一步操作，继续读取输入流的下一行，直到处理完输入流的最后一行才退出\ ``sed循环``\ ，sed处理完毕

.. _scriptll:

\ ``SCRIPT循环``\ 的处理流程是

- \ **(1)**\ 判断\ ``模式空间(pattern space)``\ 是否为空，如果为空，则跳出\ ``SCRIPT循环``\ ；如果不为空，就执行下面操作
- \ **(2)**\ 如果\ ``模式空间(pattern space)``\ 中的内容能够匹配\ ``Address1``\ ，则执行\ ``Address1``\ 对应的\ ``cmd1``\ 对内容进行处理；否则不执行\ ``cmd1``\ 
- \ **(3)**\ 如果\ ``模式空间(pattern space)``\ 中的内容能够匹配\ ``Address2``\ ，则执行\ ``Address2``\ 对应的\ ``cmd2``\ 对内容进行处理；否则不执行\ ``cmd2``\ 
- ...
- \ **(4)**\ 当判断执行完所有的\ ``cmd``\ 后，\ ``auto_print``\ 会自动输出\ ``模式空间(pattern space)``\ 中的内容到标准输出流或定向流中，并添加尾随的换行符
- \ **(5)**\ 最后\ ``remove_pattern_space``\ 会清空\ ``模式空间(pattern space)``\ 中的内容，并返回第一步操作

需要注意的是

- \ ``SCRIPT循环``\ 中的\ ``2/3步``\ 对应着我们\ `sed脚本 <#awkprograml>`_\ 部分(即语法格式中的\ ``SCRIPT``\ )，可以根据需要进行修改
- \ ``SCRIPT循环``\ 中的\ ``4/5步``\ \ ``自动输出``\ 和\ ``清空模式空间内容``\ ，这两个操作每次\ ``SCRIPT循环``\ 都会默认自动执行，但是有些\ `操作命令 <#opcmdl>`_\ 或\ `选项 <#awkoptionsl>`_\ 可以改变这两个操作行为，使其输出总是输出空内容或无法输出或无法清空模式空间等

	- \ ``D``\ 命令会进入多行模式，使得\ ``SCRIPT循环``\ 结束时将数据锁在\ ``模式空间(pattern space)``\ 中不输出也不清空，并且在当前\ ``SCRIPT循环``\ 还没结束时就强行进入下一轮\ ``SCRIPT循环``\ ，其实就相当 于在上面的while循环结构中加上了\ ``continue``\ 关键字
	- \ ``d``\ 命令可以直接跳出\ ``SCRIPT循环``\ 进入下一个\ ``sed循环``\ ，就像是在while循环中加上了\ ``break``\ 一样
	- \ ``q``\ 和\ ``Q``\ 命令可以直接退出\ ``sed循环``\ ，就像是在while循环中加上了\ ``exit``\ 一样

.. _awkoptionsl:

0x03 sed常用选项
------------------

\ ``sed``\ 命令的常用OPTIONS选项有

- \ ``--help``\ ：输出sed命令行的简单帮助信息并退出，也可以使用\ ``info sed``\ 命令查看其帮助信息，info文档更全面详细点
- \ ``-n/--quiet/--silent``\ ：默认情况下，sed将在每轮\ `SCRIPT循环 <#scriptll>`_\ 结束时自动输出模式空间中的内容。使用该选项后可以使得这次自动输出动作输出空内容。注意，该选项是输出空内容而不是禁用输出动作，前者有输出流只是输出空流，后者则没有输出流；虽然两者的结果都是不输出任何内容，但在有些依赖于输出动作和输出流的地方，它们的区别是很大的。这种情况下，只有显式通过\ ``p``\ 命令来产生对应的输出
- \ ``-e SCRIPT/--expression=SCRIPT``\ ：\ `SCRIPT <#awkprograml>`_\ 是一个包含sed命令的表达式，\ ``-e``\ 选项就是向SCRIPT中添加命令的。可以省略\ ``-e``\ 选项，但如果命令行容易产生歧义，则使用\ ``-e``\ 选项可明确说明这部分是SCRIPT中的命令。另外，如果一个\ ``-e``\ 选项不方便描述所需命令集合时，可以指定多个\ ``-e``\ 选项
- \ ``-f SCRIPT-FILE/--file=SCRIPT-FILE``\ ：指定包含sed命令集合的SCRIPT文件(即将\ ``-e``\ 选项后面的SCRIPT表达式写入文件中)，让sed根据SCRIPT文件中的命令集处理输入流
- \ ``-i[SUFFIX]/--in-place[=SUFFIX]``\ 

	- 该选项指定要将sed的输出结果保存(以覆盖的方式)到当前编辑的文件中。该项是通过创建一个临时文件并将输出写入到该临时文件，最后重命名为源文件来实现的。该选项隐含了\ ``-s``\ 选项
	- 当处理完当前输入流后，临时文件被重命名为源文件的名称，如果没有提供\ ``SUFFIX``\ ，源文件被覆盖，且不会生成备份文件；如果提供了\ ``SUFFIX``\ ，则在重命名临时文件之前，先使用该\ ``SUFFIX``\ 修改源文件名，从而生成一个备份文件(例如\ ``sed -i'.log' SCRIPT a.txt``\ 将生成两个文件\ ``a.txt``\ 和\ ``a.txt.log``\ ，前者是sed修改后的文件，\ ``a.txt.log``\ 是\ ``源a.txt``\ 的备份文件)
	- 注意：如果\ ``SUFFIX``\ 不包含符号\ ``*``\ ，将\ ``SUFFIX``\ 添加到原文件名的后面当作备份文件的后缀；如果\ ``SUFFIX``\ 中包含了一个或多个字符\ ``*``\ ，则每个\ ``*``\ 都替换为原文件名；这使得可以为备份文件添加一个前缀，而不是后缀，甚至可以将此备份文件放在在其他已存在的目录下
- \ ``-r/--regexp-extended``\ ：使用\ `扩展正则表达式 <../../../../../5-Wildcard/2-Regular/1-syntax/2-eRegEx.html>`_\ ，而不是使用默认的\ `基础正则表达式 <../../../../../5-Wildcard/2-Regular/1-syntax/1-bRegEx.html>`_\ ；sed所支持的扩展正则表达式和egrep一样，使用扩展正则表达式显得更简洁，因为有些元字符不用再使用反斜线\ ``\``\ ，但这是GNU扩展功能，因此应避免在可移植性脚本中使用
- \ ``-s/--separate``\ ：默认情况下，如果为sed指定了多个输入文件，例如\ ``sed OPTIONS SCRIPT file1 file2 file3``\ ，则多个文件会被sed当作一个长的输入流处理，也就是说所有文件被当成一个大文件进行处理。指定该选项后，sed将认为命令行中给定的每个文件都是独立的输入流；既然是独立的输入流，\ `范围定界 <#scopel>`_\ (如\ ``/abc/``\ ,\ ``/def/``\ )就无法跨越多个文件进行匹配，行号也会在处理每个文件时重置，\ ``$``\ 代表的也将是每个文件的最后一行
- \ ``-l N/--line-length=N``\ ：为\ ``l``\ 命令指定默认的换行长度。\ ``N=0``\ 意味着完全不换行的长行，如果不指定，则70个字符就换行
- \ ``--follow-symlinks``\ ：该选项只在支持符号连接的操作系统上生效，且只有指定了\ ``-i``\ 选项时才生效。指定该选项后，如果sed命令行中指定的输入文件是一个符号连接，则sed将对该符号链接的目标文件进行处理。默认情况下，禁用该选项，因此不会修改链接的源文件
- \ ``-u/--unbuffered``\ :使用尽量少的空间缓冲输入和输出行；该选项在某些情况下尤为有用，例如输入流的来源是\ ``tail -f``\ 时，指定该选项将可以尽快返回输出结果
- \ ``-z/--null-data/--zero-terminated``\ ：以空串符号\ ``\0``\ 而不是换行符\ ``\n``\ 作为输入流的行分隔符



.. _awkprograml:

0x04 sed程序
--------------

.. _scopel:

0x0400 sed程序之范围定界
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _opcmdl:

0x0401 sed程序之操作命令
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. _scriptl:

0x05 sed脚本
-------------

.. _sedinputl:

0x06 sed输入流
--------------

.. _applyl:

0x06 sed应用
-------------