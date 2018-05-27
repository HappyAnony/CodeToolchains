常见操作
==========

1.查看内存空间使用状态

- \ ``cat /proc/meninfo``\ 
- \ ``free``\ (-m:显示结果以MB位单位；-g:显示结果以GB为单位) 
- \ ``vmstat -s``\ 

2.查看当前系统挂载信息

- \ ``mount``\ 
- \ ``cat /proc/mounts``\ 
- \ ``cat /etc/mtab``\ 

3.查看整个目录文件 (包含目录下文件和子目录 )的大小

- `du -s /path/to/dir`

4.查看挂载磁盘的分区信息

- \ ``fdisk -l dev``\ 查看指定磁盘的分区信息
- \ ``cat /proc/partitions``\ 查看所有挂载磁盘的分区信息

5.查看超级块SuperBlock的信息

- \ ``dumpe2fs -h /deb/sdb``\ 
- \ ``tune2fs -l /deb/sdb``\ 

6.启动图形界面

- \ ``startx``\ 启动Gnome界面
- \ ``startkde``\ 启动KDE界面
- \ ``Ctrl+Alt+F7``\ 启动图形界面

7.取消正在执行的命令 

- \ ``Ctrl+c``\ 当执行startx时可以使用该命令退出图形界面

8.立即释放命令提示符(后台执行) 

- \ ``startx &``\ 可以在启动完图形界面后释放命令提示符

9.翻屏

- \ ``shift+PageUp/PageDown``\ 

10.调用上一条命令后面的参数

- 使用\ ``esc+.``\ ，先\ ``esc键``\ 然后\ ``点号``\ 

11.在交互模式下回删字符

- \ ``ctrl+BackSpace``\ 

12.查看端口对应的服务名或者服务对应的端口 

- \ ``grep ssh /etc/services``\ ，/etc/services是一个端口名称解析库

13.通过查看服务组件的生成文件查看服务的服务名

- \ ``rpm -ql telnet-server``\ 

14.关闭GUI 

- \ ``init 3``\ 
- \ ``Alt+Ctrl+F1~F6``\ 
- 修改配置文件

15.启动GUI

- \ ``init 5``\ 
- \ ``startx``\ 
- 修改配置文件