目录文件
==========

命令汇总

- \ `mkdir <#mkdirl>`_\ 
- \ `rm <#rml>`_\ 
- \ `mv <#movel>`_\ 
- \ `cp <#copyl>`_\ 
- \ `find <#findl>`_\ 
- \ `locate <#findl>`_\ 
- \ `ln <#linkl>`_\ 
- \ `cd <#cdl>`_\ 
- \ `pwd <#cdl>`_\ 
- \ `tar <#tarl>`_\  
- \ `zip <#zipl>`_\ 
- \ `unzip <#unzipl>`_\ 
- \ `ls <#lsl>`_\ 




目录文件的基本操作有

- \ `目录创建 <#dcreat>`_\ 
- \ `目录删除 <#ddel>`_\
- \ `目录移动 <#dmove>`_\ 
- \ `目录复制 <#dcopy>`_\ 
- \ `目录查找 <#dfilefind>`_\ 
- \ `目录链接 <#dlink>`_\ 
- \ `目录切换 <#dchange>`_\ 
- \ `目录归档 <#darchive>`_\ 
- \ `目录压缩 <#dcompress>`_\ 
- \ `目录解压 <#dexpand>`_\ 
- \ `目录查看 <#look>`_\ 

.. _dcreat:

0x00 目录创建 
~~~~~~~~~~~~~~

.. _mkdirl:

我们通常使用\ ``mkdir命令``\ 来创建目录

.. code-block:: sh

	$mkdir test                # 如果目录存在将会提示错误
	$mkdir -pv test/docker     # -p表示如果父目录不存在则先创建父目录再创建子目录，父目录存在也不会提示错误；-v表示显示创建的过程
	$mkdir -m=777 test         # 指定创建目录的权限模型为777

	# 创建级联目录
	$mkdir -pv baklog/{bin/{test,docker},lib,log/{cep,dod,testlog}}  # 同一级目录名放在同一个大括号里，目录名用逗号分隔，但是之前不能留有空白

.. _ddel:

0x01 目录删除
~~~~~~~~~~~~~~~~

.. _rml:

目录删除使用\ ``rm``\ 命令

.. code-block:: sh

	$rm -rf test            # 强制删除test目录以及其子目录或文件


.. _dmove:

0x02 目录移动
~~~~~~~~~~~~~~~

.. _movel:

目录移动使用\ ``move``\ 命令，可参考\ `文件移动mv <../1-File/index.html#mvl>`_\ 

.. code-block:: sh

	$mv baklog/ test       # 将baklog目录以及子目录或文件移到到test目录下，此处可以添加-r选项，可以不添加，默认是递归移动


.. _dcopy:

0x03 目录复制
~~~~~~~~~~~~~~~~

.. _copyl:

目录移动使用\ ``cp``\ 命令，可参考\ `文件移动cp <../1-File/index.html#cpl>`_\ 

.. code-block:: sh

	$cp -r baklog/ bak    # 将baklog目录下子目录或者文件复制到bak目录下，此处需要使用-r选项才能实现递归复制

.. _dfilefind:

0x04 目录查找
~~~~~~~~~~~~~~~~~

.. _findl:

目录查找使用\ ``find``\ 命令和\ ``locate``\ 命令，可参考\ `文件查找find <../1-File/index.html#findl>`_\ 和\ `文件查找locate <../1-File/index.html#locatel>`_\  


.. _dlink:

0x05 目录链接
~~~~~~~~~~~~~~~

.. _linkl:

目录只能创建软链接，不能创建硬链接，同样使用\ ``ln``\ 命令，可参考\ `文件链接ln <../1-File/index.html#linkl>`_\ 


.. _dchange:

0x06 目录切换
~~~~~~~~~~~~~~~

.. _cdl:

我们可以通过\ ``cd``\ 命令实现目录的切换

.. code-block:: sh

	$cd test           # 切换到test目录下
	$cd -              # 切换到上一工作目录
	$cd ~              # 切换到家目录
	$cd ..             # 切换到上级父目录
	$cd ../../         # 切换到父目录的父目录
	$pwd               # 显示当前路径


.. _darchive:

0x07 目录归档
~~~~~~~~~~~~~~~

.. _tarl:

我们知道\ ``gzip``\ 、\ ``bzip2``\ 、\ ``xz``\ 三个压缩工具只能对文件进行压缩，不能对目录进行压缩；此时我们可以通过\ ``tar``\ 工具将目录进行归档打包成一个文件，便于这三个压缩工具对目录进行压缩

\ ``tar``\ 命令的使用语法为：\ ``tar [OPTION...] [FILE]...``\ ，其中常用的OPTIONS有

- \ ``-c``\ ：创建归档
- \ ``-f``\ ：指定归档后的文件名。归档后的文件名必须跟在该选项后面
- \ ``-x``\ ：展开归档
- \ ``-v``\ ：显示命令执行的过程
- \ ``-t``\ ：不展开而直接查看被归档的文件
- \ ``-z``\ ：使用gzip压缩
- \ ``-j``\ ：使用bzip2压缩
- \ ``-J``\ ：使用xz压缩

常见用法有

.. code-block:: sh

	# 该工具属于归档压缩工具(先对文件进行归档然后进行压缩)，所有不会在压缩或者解压时删除原文件

	$tar -cf archive.tar foo bar     # 将foo和bar进行归档成archive.tar文件(foo和bar可以是目录或者文件)
	$tar -tvf archive.tar            # 不展开archive归档文件直接列出其中的文件列表
	$tar -xf archive.tar             # 展开archive归档文件
	$tar -zcf archive.tar.gz FILE    # 将FILE目录或文件先进行归档，然后使用gzip将其压缩成.gz压缩文件
	$tar -jcf archive.tar.bz2 FILE   # 将FILE目录或文件先进行归档，然后使用bzip2将其压缩成.bz压缩文件
	$tar -Jcf archive.tar.xz FILE    # 将FILE目录或文件先进行归档，然后使用xz将其压缩成.xz压缩文件
	$tar -zxf archive.tar.gz         # 将指定的.gz压缩文件先使用gzip -d解压缩，然后将归档文件展开成对应的原文件或目录
	$tar -jxf archive.tar.bz2        # 将指定的.bz2压缩文件先使用bzip2 -d解压缩，然后将归档文件展开成对应的原文件或目录
	$tar -Jxf archive.tar.xz         # 将指定的.xz压缩文件先使用xz -d解压缩，然后将归档文件展开成对应的原文件或目录

.. _dcompress:

0x08 目录压缩
~~~~~~~~~~~~~~~

.. _zipl:

此处目录压缩有两种方法

- 使用\ ``zip``\ 压缩工具
- 使用\ ``tar``\ 归档工具

.. code-block:: sh

	# 使用tar压缩目录
	# 该工具属于归档压缩工具(先对文件进行归档然后进行压缩)，所有不会在压缩或者解压时删除原文件
	$tar -zcf archive.tar.gz FILE    # 将FILE目录或文件先进行归档，然后使用gzip将其压缩成.gz压缩文件
	$tar -jcf archive.tar.bz2 FILE   # 将FILE目录或文件先进行归档，然后使用bzip2将其压缩成.bz压缩文件
	$tar -Jcf archive.tar.xz FILE    # 将FILE目录或文件先进行归档，然后使用xz将其压缩成.xz压缩文件

	# 使用zip压缩目录
	# 该工具属于归档压缩工具(先对文件进行归档然后进行压缩)，所有不会在压缩或者解压时删除原文件
	# 当.zip文件存在时，归档压缩模式类似于文件打开的追加模式，存在的文件则会覆盖，不存在的则会新建添加
	$zip pam.d.zip pam.d/*           # 将pam.d目录下所有目录或文件先归档然后压缩成pam.d.zip文件，不包括pam.d目录名，同时不会进行目录递归
	$zip pam.d.zip pam.d/            # 只将pam.d目录名进行归档然后压缩成pam.d.zip文件，不包括其下的子目录和文件
	$zip -r pam.d.zip pam.d/         # 将pam.d目录名以及其所有子目录或者文件先归档然后压缩成pam.d.zip文件，此时会进行目录递归


.. _dexpand:

0x09 目录解压
~~~~~~~~~~~~~~~~

\ ``tar``\ 工具只能解压\ ``.gz``\ 、\ ``.bz2``\ 、\ ``.xz``\ 格式的压缩文件

.. code-block:: sh

	# 该工具属于归档压缩工具(先对文件进行归档然后进行压缩)，所有不会在压缩或者解压时删除原文件
	$tar -zxf archive.tar.gz         # 将指定的.gz压缩文件先使用gzip -d解压缩，然后将归档文件展开成对应的原文件或目录
	$tar -jxf archive.tar.bz2        # 将指定的.bz2压缩文件先使用bzip2 -d解压缩，然后将归档文件展开成对应的原文件或目录
	$tar -Jxf archive.tar.xz         # 将指定的.xz压缩文件先使用xz -d解压缩，然后将归档文件展开成对应的原文件或目录

.. _unzipl:

\ ``.zip``\ 压缩文件只能使用\ ``unzip``\ 工具实现解压

.. code-block:: sh

	# 该工具属于归档压缩工具(先对文件进行归档然后进行压缩)，所有不会在压缩或者解压时删除原文件
	$unzip message.zip


.. _look:

0x10 目录查看
~~~~~~~~~~~~~~~

.. _lsl: 

我们可以通过\ ``ls``\ 命令来查看目录中的内容

.. code-block:: sh

	$ls               # 列出当前目录下的内容(不包括隐藏文件)
	$ls -a            # 列出当前目录下所有有文件，包含隐藏文件
	$ls -A            # 列出当前目录下所有文件，但不显示.(当前目录)和..(上级目录)通用路径
	$ls --color[=never|auto|always]  # 显示颜色
	$ls -l            # 长格式显示
	$ls -d            # 只显示当前目录(.)自身的属性，通常与-l同时使用
	$ls -r            # 实现逆序显示
	$ls -R            # 实现递归显示，效果跟tree命令很相似，显示子目录中的子目录或者文件
	$ls -i            # 显示目录下各文件对应的存储在元数据区的inode索引号
	$ls -h            # 文件大小以人类易读格式显示