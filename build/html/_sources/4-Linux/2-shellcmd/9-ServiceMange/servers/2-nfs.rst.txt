nfs服务器
==========

参考文档：

- \ `centos7安装nfs服务器 <http://blog.51cto.com/12173069/2068815>`_\ 
- \ `ubuntu nfs服务的搭建 <http://www.cnblogs.com/MoreExcellent/p/7222895.html>`_\ 

\ ``ftp服务器``\ 的全称是\ ``net file system``\ 网络问卷系统，它的主要功能就是：允许网络中计算机之间能够通过TCP/IP网络共享资源

\ ``nfs服务器的搭建``\ 我们这里选择使用

- CentOS下：\ ``nfs-utils``\ 和\ ``rpcbind``\ 
- Ubuntu下：\ ``nfs-common``\ 

其搭建基本流程是：

- \ `nfs工具安装 <#nfsl>`_\ 

	- \ `Ubuntu系列安装 <#nfsubuntu>`_\ 
	- \ `CentOS系列安装 <#nfscentos>`_\ 
- \ `服务端配置 <#nfsserver>`_\ 
	
	- \ `Ubuntu系列配置 <#ubuntuconf>`_\ 
	- \ `CentOS系列配置 <#centosconf>`_\ 
- \ `客户端挂载 <#nfsclient>`_\ 

	- \ `Ubuntu系列挂载 <#ubuntuconfl>`_\ 
	- \ `CentOS系列挂载 <#centosconfl>`_\ 

.. _nfsl:

0x00 nfs工具安装
~~~~~~~~~~~~~~~~~~~~~

不同linux发行版的安装方式不同

.. _nfsubuntu: 

Debian/Ubuntu系列安装方式

.. code-block:: sh

	# apt-get安装
	$sudo apt-get install nfs-common

.. _nfscentos: 

RedHat/Fedra/CentOs系列安装方式

.. code-block:: sh
	
	# yum安装
	$yum install nfs-utils
	$yum install rpcbind


.. _nfsserver: 

0x01 服务端配置
~~~~~~~~~~~~~~~~~

.. _ubuntuconf:


.. _centosconf: 

CentOS系列服务端配置如下：

- 创建共享目录；\ ``mkdir /home/test/shareDir``\  
- 更改目录权限：\ ``chmod  -R  a+w  shareDir/``\
- 更改配置文件：\ ``vim /etc/exports``\ 添加\ ``/home/test/shareDir 192.168.80.*(rw,async,no_root_squash)``\ ，每个字段的意思是：

	- \ ``/home``\ ：表示需要共享的目录
	- \ ``192.168.80.*``\ ：指定哪些用户可以访问

		- \ ``*``\ ：所有可以ping同该主机的用户
		- \ ``192.168.1.*``\ ：指定网段，在该网段中的用户可以挂载
		- \ ``192.168.1.12``\ ：只有该用户能挂载
	- \ ``(ro,sync,no_root_squash)``\ ：权限

		- \ ``ro``\ : 只读
		- \ ``rw``\ : 读写
		- \ ``sync``\ : 同步
		- \ ``no_root_squash``\ ：不降低root用户的权限
	- 其它信息可以通过\ ``man 5 exports``\ 进行查看
- 启动服务

	- \ ``systemctl  start  rpcbind``\ 
	- \ ``systemctl  start  nfs``\ 
- 开启rpcbind防火墙端口

	- \ ``iptables  -I  INPUT  -p  udp  --dport  111  -j  ACCEPT``\ 
	- \ ``iptables  -I  INPUT  -p  tcp  --dport  111  -j  ACCEPT``\ 
- 开发nfs防火墙端口

	- \ ``iptables  -I  INPUT  -p  udp  --dport  2049  -j  ACCEPT``\ 
	- \ ``iptables  -I  INPUT  -p  tcp  --dport  2049  -j  ACCEPT``\ 


最后我们通过\ ``showmount  -e  192.168.80.128``\ 命令来查看服务端状态，确认之前创建的目录是否被共享

.. figure:: ../images/8.png

.. _nfsclient: 

0x02 客户端挂载
~~~~~~~~~~~~~~~~

.. _ubuntuconfl:


.. _centosconfl: 

CentOS系列客户端配置如下：

- 手动将共享目录挂载到本地指定目录下

	- \ ``mount  192.168.80.128:/home/test/shareDir  /tmp/test/``\ 
- 配置开机自动挂载

	- \ ``echo  "192.168.80.128:/home/test/shareDir  /tmp/test/  nfs  defaults  0  0"  >>  /etc/fstab``\ 

- 共享测试

	- 客户端创建文件：\ ``touch /tmp/test/1.txt``\ 
	- 服务端是否同步：\ ``ll /home/test/shareDir``\ 

.. figure:: ../images/9.png