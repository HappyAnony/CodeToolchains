控制流程语句
=================

和其它编程语言一样，shell的控制流程语句大体上也分为三种

- \ `顺序执行语句 <#orderstate>`_\ 
- \ `条件执行语句 <#conditionstate>`_\ 

	- \ `if条件语句 <#ifconditon>`_\ 
	- \ `case条件语句 <#casecondition>`_\ 
	- \ `select条件语句 <#selectcondition>`_\ 
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

.. _casecondition:

0x0101 case条件语句
~~~~~~~~~~~~~~~~~~~~~

.. _selectcondition:

0x0102 select条件语句
~~~~~~~~~~~~~~~~~~~~~~~

.. _loopstate:

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


