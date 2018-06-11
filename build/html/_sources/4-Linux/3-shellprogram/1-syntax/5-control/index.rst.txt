控制流程语句
=================

和其它编程语言一样，shell的控制流程语句大体上也分为三种

- \ `顺序执行语句 <#orderstate>`_\ 
- \ `条件执行语句 <#conditionstate>`_\ 

	- \ `if条件语句 <#ifconditon>`_\ 
	- \ `case条件语句 <#casecondition>`_\ 
	- \ `select条件语句 <#selectcondition>`_\ 
	- \ `条件测试表达式 <#conteststate>`_\ 
- \ `循环执行语句 <#loopstate>`_\ 

	- \ `for循环语句 <#forloop>`_\ 
	- \ `while循环语句 <#whileloop>`_\ 
	- \ `until循环语句 <#untilloop>`_\ 

.. _orderstate:

0x00 顺序执行语句
~~~~~~~~~~~~~~~~~~~~

顺序执行语句是默认法则，即按照自上而下、自左往右的顺序逐条执行各命令，每执行一次就会得到对应的结果，然后退出该次执行操作

.. _conditionstate:

0x01 条件执行语句
~~~~~~~~~~~~~~~~~~~~

条件执行语句会根据判断条件选择符合条件的分支执行对应的\ ``cmd_list``\ 命令列表，执行完命令后就会退出该分支；条件执行语句有以下几种

- \ `if条件语句 <#ifconditon>`_\ 
- \ `case条件语句 <#casecondition>`_\ 
- \ `select条件语句 <#selectcondition>`_\ 

.. _ifconditon:

0x0100 if条件语句
~~~~~~~~~~~~~~~~~~~

if条件语句的语法结构如下(使用\ ``help if``\ 命令可以查看)

.. code-block:: sh

	if TEST_COMMANDS; then
		COMMANDS_LIST;
	[elif TEST_COMMANDS; then
		COMMANDS_LIST;]
	...
	[else
		COMMANDS_LIST;]
	fi

其执行逻辑是

- \ **1.**\  先执行\ ``if``\ 分支下的\ ``TEST_COMMANDS``\ 条件测试命令，如果执行完的状态返回值为\ ``非0``\ ，则执行第2步；如果执行完的状态返回值为\ ``0``\ ，即\ ``TEST_COMMANDS``\ 条件测试命令执行成功，则执行该分支下的\ ``COMMANDS_LIST``\ 命令列表，执行完后就直接退出，此时整个if语句结构体的状态返回值取决于\ ``COMMANDS_LIST``\ 命令列表中最后一个命令的状态返回值
- \ **2.**\ 如果存在\ ``elif``\ 分支，则按照第一步的流程依次执行\ ``elif``\ 分支下的\ ``TEST_COMMANDS``\ 条件测试命令，如果没有一个\ ``elif``\ 分支的状态返回值为\ ``0``\ ，则执行第3步；如果存在一个\ ``elif``\ 分支的状态返回值为\ ``0``\ ，即该分支下的\ ``TEST_COMMANDS``\ 条件测试命令执行成功，则执行该分支下的\ ``COMMANDS_LIST``\ 命令列表，执行完后就直接退出，此时整个if语句结构体的状态返回值取决于\ ``COMMANDS_LIST``\ 命令列表中最后一个命令的状态返回值
- \ **3.**\ 如果\ ``else``\ 分支不存在，那么整个if语句结构体的状态返回值为\ ``0``\ ；如果存在\ ``else``\ 分支，则执行该分支下的\ ``COMMANDS_LIST``\ 命令列表，执行完后就直接退出，此时整个if语句结构体的状态返回值取决于\ ``COMMANDS_LIST``\ 命令列表中最后一个命令的状态返回值

在整个if语句结构体中有两个地方需要注意

- \ ``COMMANDS_LIST``\ ：表示待执行的命令列表，即一系列shell命令的集合，类型格式多种多样，在一系列示例代码中可见一斑
- \ ``TEST_COMMANDS``\ ：表示条件测试命令，即通过引用条件测试命令的执行状态返回值是否为\ ``0``\ 来判断是否执行上述\ ``COMMANDS_LIST``\ 命令列表；\ **这里需要特别注意的是，和其它语言不通，shell的条件测试命令只有以下三种类型**\ 

	- \ ``命令执行``\ ：命令本身执行后就会产生对应的执行状态返回值，所以可以直接用来做条件判断

		- 此时不能使用\ ``````\ 操作符来引用命令，因为该操作引用的是命令的执行结果，而不是命令的执行状态返回值
		- 通常是直接使用命令，然后在命令后面添加\ ``s&> /dev/null``\ ，表示将命令的执行结果重定向至\ ``/dev/null``\ ，只引用其状态返回值；例如：\ ``if grep "^root" /etc/passwd &> /dev/null; then``\ 
	- \ ``执行条件测试表达式``\ ：在shell中，条件测试表达式是由条件测试操作符以及对应的操作数组成，详细介绍可参考下列：\ `条件测试表达式 <#conteststate>`_\ ，执行条件测试表达式有以下三种格式

		- \ ``test Test_Expression``\ ：通过\ ``test``\ 命令执行
		- \ ``[ Test_Expression ]``\ ：通过\ ``[]``\ 操作符执行，注意\ ``Test_Expression`` 前后有空格
		- \ ``[[ Test_Expression ]]``\ ：通过\ ``[[]]``\ 操作符执行，注意\ ``Test_Expression`` 前后有空格
	- \ ``组合条件测试``\ ：即对多个\ ``命令执行状态返回值``\ 或者\ ``执行条件测试表达式状态返回值``\ 做逻辑运算，组合条件测试有以下三种格式

		- 逻辑与操作：只有当\ ``&&``\ 操作符两边执行结果都为真(状态值为0)，最后组合条件测试结果才为真(状态值为0)

			- \ ``[ Test_Expression1 ] && [ Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``COMMAND &> /dev/null && [ Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``COMMAND1 &> /dev/null && COMMAND2 &> /dev/null &&``\ 
			- \ ``[ Test_Expression1 -a Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``[[ Test_Expression1 && Test_Expression2 ]]``\ ：此处只能使用\ ``[[]]``\ 操作符，因为\ ``&&``\ 运算符不允许用于\ ``[]``\ 操作符中
		- 逻辑或操作：只要\ ``||``\ 操作符两边执行结果有一个为真(状态值为0)，最后组合条件测试结果就为真(状态值为0)

			- \ ``[ Test_Expression1 ] || [ Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``COMMAND &> /dev/null || [ Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``COMMAND1 &> /dev/null || COMMAND2 &> /dev/null &&``\ 
			- \ ``[ Test_Expression1 -0 Test_Expression2 ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``[[ Test_Expression1 || Test_Expression2 ]]``\ ：此处只能使用\ ``[[]]``\ 操作符，因为\ ``||``\ 运算符不允许用于\ ``[]``\ 操作符中
		- 逻辑非操作：对\ ``!``\ 右侧执行结果取反

			- \ ``! [ Test_Expression ]``\ ：此处使用\ ``[]``\ 或\ ``[[]]``\ 都行
			- \ ``! COMMAND1 &> /dev/null``\ 
			- \ ``! ([ Test_Expression1 ] || [ Test_Expression2 ])``\ ：此处相当于\ ``! [ Test_Expression1 ] && ! [ Test_Expression2 ]``\ 
			- \ ``! ([ Test_Expression1 ] && [ Test_Expression2 ])``\ ：此处相当于\ ``! [ Test_Expression1 ] || ! [ Test_Expression2 ]``\ 
		- 注意：\ **非的优先级大于与，与的优先级大于或**\ 


.. _casecondition:

0x0101 case条件语句
~~~~~~~~~~~~~~~~~~~~~

.. _selectcondition:

0x0102 select条件语句
~~~~~~~~~~~~~~~~~~~~~~~

.. _loopstate:


.. _conteststate:

0x0103 条件测试表达式
~~~~~~~~~~~~~~~~~~~~~~


0x02 循环执行语句
~~~~~~~~~~~~~~~~~~~

循环执行语句会根据判断条件循环多次执行对应的循环体\ ``cmd_list``\ 命令列表，当判断条件不满足时就会退出该循环体；循环执行语句有以下几种

- \ `for循环语句 <#forloop>`_\ 
- \ `while循环语句 <#whileloop>`_\ 
- \ `until循环语句 <#untilloop>`_\ 

.. _forloop:

0x0200 for循环语句
~~~~~~~~~~~~~~~~~~~~

.. _whileloop:

0x0201 while循环语句
~~~~~~~~~~~~~~~~~~~~~~~

.. _untilloop:

0x0202 until循环语句
~~~~~~~~~~~~~~~~~~~~~


