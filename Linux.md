* [硬链接和软连接的区别](#硬链接和软连接的区别)
* [Linux 用过的命令](#linux-用过的命令)


## 硬链接和软连接的区别

我们可以把一个磁盘分成一个或多个分区，每个分区可以包含一个文件系统。

磁盘、分区和文件系统结构图：
<div align="center"> <img src="./imgs/linux/file_system1.jpg"/> </div><br>

#### 硬链接

硬链接示意图：
<div align="center"> <img src="./imgs/linux/link.jpg"/> </div><br>

* 图中有两个目录指向同一个i节点，每个i节点都有一个连接计数，其值是指向该i节点的目录项数。只有当链接计数为0时，才可删除该文件（释放数据块）。这种链接被称为“硬链接”。
* i节点包含了大多数与文件有关的信息：文件类型、文件访问权限位、文件长度等。
* 每个文件系统各自对它们的i节点进行编号，一个文件系统的的目录不能指向另一个文件系统的i节点。

创建一个指向现有文件的链接方法时使用link函数：
~~~c
int link(const char *existingpath, const char *newpath);
~~~

删除一个现有目录项，可以调用unlink函数：
~~~c
int unlink(const char *pathname);
~~~
<br>

创建硬链接命令：
~~~shell
$ ln existingpath newpath 
~~~

创建软链接命令：
~~~shell
$ ln -s existingpath newpath
~~~

#### 符号链接

符号链接是指向一个文件的间接指针，保存着源文件所在的绝对路径，在读取时会定位到源文件上，可以理解为 Windows 的快捷方式。当源文件被删除了，链接文件就打不开了。

引入符号链接的原因是为了避开硬链接的一些限制：
* 硬链接通常要求链接和文件位于同一个文件系统中。
* 只有超级用户才能创建指向目录的硬链接。

当使用以名字引用文件的函数时，应当了解该函数是否处理符号链接。也就是该函数是否跟随符号链接到达它所链接的文件。若有符号处理功能，则其路径名参数引用由符号链接指向的文件。否则，路径名参数引用链接本身。

使用符号链接在系统中可能引入循环，如实例：
~~~shell
$ mkdir foo                  创建一个新目录
$ touch foo/a                创建0长文件
$ link -s ../foo foo/testdir 创建符号链接
$ ls -l foo
$ ftw(3)
~~~

构成循环符号链接：
<div align="center"> <img src="./imgs/linux/symlink.jpg"/> </div><br>

使用ftw(3)遍历文件结构，输出是：
~~~shell
foo
foo/a
foo/testdir
foo/testdir/a
foo/testdir/testdir
foo/testdir/testdir/a
foo/testdir/testdir/tsetdir
foo/testdir/testdir/testdir/a
~~~

使用unlink可以消除循环，因为unlink并不跟随符号链接。但是创建一个构成这种循环的硬链接，就很难消除它。这就是为什么link函数不允许构造指向目录的硬链接的原因（除非进程具有超级特权）。

在使用处理符号链接的函数的时候，如果链接指定的文件不存在，那么返回出错。

## Linux 用过的命令

#### ps

ps命令用于显示当前进程的状态。

查看用户自己的进程:
~~~shell
$ ps -l
~~~


查看系统所有进程包含系统其它使用者，较详细的信息：
~~~shell
$ ps aux
$ ps -ef   与上面的命令结果一样
~~~

查看特定的进程：
~~~shell
$ ps aux | grep processX
~~~

根据 CPU 使用来升序排序:
~~~shell
$ ps -aux --sort -pcpu | less
~~~

根据内存使用来升序排序
~~~shell
$ ps -aux --sort -pmem | less
~~~


#### kill

kill命令用于删除执行中的程序或工作。

预设的信号为SIGTERM(15)，可将指定程序终止：
~~~shell
$ kill 12345
~~~

强制删除程序：
~~~shell
$ kill -9 12345
~~~

杀死指定用户所有进程:
~~~shell
$ kill -9 $(ps -ef | grep hnlinux) //方法一 过滤出hnlinux用户进程 
$ kill -u hnlinux //方法二
~~~

某个进程杀不掉的原因?
* 进程已经成为僵死进程，当它的父进程将它回收或将它的父进程kill掉即可在ps输出看不到了。
* 进程正处在内核状态中，Linux进程运行时分内核和用户两种状态，当进程进入内核状态后，会屏蔽所有信号，包括SIGKIL，所以这个时候kill -9也变得无效了。

#### touch

touch命令用于修改文件或者目录的时间属性，包括存取时间和更改时间。若文件不存在，系统会建立一个新的文件。

#### chown

利用 chown 将指定文件的拥有者改为指定的用户或组.

将文件 file1.txt 的拥有者设为 users 群体的使用者 runoob :
~~~shell
$ chown users:runoob file1.txt
~~~

将目前目录下的所有文件与子目录的拥有者皆设为 users 群体的使用者 lamport :
~~~shell
$ chown -R lamport:users *
~~~

#### cat/tac

cat 命令用于连接文件并打印到标准输出设备上。

把 textfile1 的文档内容加上行号后输入 textfile2 这个文档里：
~~~shell
$ cat -n textfile1 > textfile2
~~~

把 textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里：
~~~shell
$ cat -b textfile1 textfile2 >> textfile3
~~~

清空 /etc/test.txt 文档内容：
~~~shell
$ cat /dev/null > /etc/test.txt
~~~

#### more/less

more可以以一页一页的方式显示内容,less比more更有弹性,可以回退之前显示的内容.

从第 20 行开始显示 testfile 之文档内容。
~~~shell
$ more +20 testfile
~~~

more中的操作命令:
~~~
Enter 向下n行，需要定义。默认为1行
Ctrl+F 向下滚动一屏
空格键 向下滚动一屏
Ctrl+B 返回上一屏
= 输出当前行的行号
：f 输出文件名和当前行的行号
V 调用vi编辑器
!命令 调用Shell，并执行命令
q 退出more

~~~

#### head/tail

head 取得文件前几行,是 head 的反向操作，只是取得是后几行。

tail -f filename会把 filename 文件里的最尾部的内容显示在屏幕上，并且不断刷新，只要 filename 更新就可以看到最新的文件内容。
~~~shell
$ tail -f notes.log
~~~

从第 20 行至文件末尾:
~~~shell
$ tail +20 notes.log
~~~

示文件 notes.log 的最后 10 个字符:
~~~shell
$ tail -c 10 notes.log
~~~

#### find

find 命令用来在指定目录下查找文件．

将目前目录及其子目录下所有延伸档名是 c 的文件列出来。
~~~shell
$ find . -name "*.c"
~~~

将目前目录其其下子目录中所有一般文件列出.
~~~shell
$ find . -type f
~~~

将目前目录及其子目录下所有最近 20 天内更新过的文件列出.
~~~shell
$ find . -ctime -20
~~~

查找/var/log目录中更改时间在7日以前的普通文件，并在删除之前询问它们：
~~~shell
$ find /var/log -type f -mtime +7 -ok rm {} \;
~~~

查找前目录中文件属主具有读、写权限，并且文件所属组的用户和其他用户具有读权限的文件：
~~~shell
$ find . -type f -perm 644 -exec ls -l {} \;
~~~


参考：https://github.com/CyC2018/CS-Notes/blob/master/notes/Linux.md#%E6%8C%87%E4%BB%A4%E4%B8%8E%E6%96%87%E4%BB%B6%E6%90%9C%E7%B4%A2

#### grep

grep 命令用于查找文件里符合条件的字符串。

在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行：
~~~shell
$ grep test *.file
~~~

以递归的方式查找符合条件的文件：
~~~shell
$ grep -r update /etc/acpi 
~~~

反向查找。前面各个例子是查找并打印出符合条件的行，通过"-v"参数可以打印出不符合条件行的内容：
~~~shell
$ grep -v test *.file
~~~

参考：https://github.com/CyC2018/CS-Notes/blob/master/notes/Linux.md#grep

#### top

top命令用于实时显示 process 的动态。

设置信息更新时间:
~~~shell
$ top -d 3
~~~

显示更新十次后退出：
~~~shell
$ top -n 10
~~~

将更新显示二次的结果输入到名称为 top.log 的档案里：
~~~shell
top -n 2 -b > top.log
~~~

#### df/du

df命令用于显示目前在Linux系统上的文件系统的磁盘使用情况统计。

以容易阅读的方式显示磁盘使用状况:
~~~shell
df -h
~~~

du命令用于显示目录或文件的大小。
~~~shell
du log2012.log 
~~~

#### netstat

netstat命令用于显示网络状态。

参数：
~~~shell
-a或--all        显示所有连线中的Socket。
-p或--programs   显示正在使用Socket的程序识别码和程序名称。
-t或--tcp        显示TCP传输协议的连线状况。
-u或--udp        显示UDP传输协议的连线状况。
-l或--listening  显示监控中的服务器的Socket。
-s或--statistice 显示网络工作信息统计表。
-n或--numeric    直接使用IP地址，而不通过域名服务器。
~~~

显示详细的网络状况：
~~~shell
$ netstat -a
~~~

列出所有端口 (包括监听和未监听的):
~~~shell
$ netstat -a     #列出所有端口
$ netstat -at    #列出所有tcp端口
$ netstat -au    #列出所有udp端口
~~~

列出所有处于监听状态的 Sockets:
~~~shell
$ netstat -l     #只显示监听端口
$ netstat -lt    #只列出所有监听 tcp 端口
$ netstat -lu    #只列出所有监听 udp 端口
$ netstat -lx    #只列出所有监听 UNIX 端口
~~~

显示每个协议的统计信息:
~~~shell
$ netstat -s    #显示所有端口的统计信息
$ netstat -st   #显示TCP端口的统计信息
$ netstat -su   #显示UDP端口的统计信息
~~~

找出程序运行的端口:
~~~shell
$ netstat -ap | grep ssh
~~~

找出运行在指定端口的进程：
~~~shell
$ netstat -tunlp | grep 80
~~~

#### wc

wc命令用于计算字数。

选项：
~~~shell
-c或--bytes或--chars 只显示Bytes数。
-l或--lines 只显示行数。
-m 统计字符数。这个标志不能与 -c 标志一起使用。
-w或--words 只显示字数。一个字被定义为由空白、跳格或换行字符分隔的字符串。
~~~

显示结果为3个数字分别表示testfile文件的行数、单词数，以及该文件的字节数:
~~~shell
$ wc testfile

3 92 598 testfile       # testfile文件的行数为3、单词数92、字节数598 
~~~


#### ls

统计当前文件夹下文件的个数:
~~~shell
$ ls -l | grep ^- | wc -l
~~~

统计当前文件夹下目录的个数:
~~~shell
$ ls -l | grep ^d | wc -l
~~~

统计当前文件夹下文件的个数，包括子文件夹里的 :
~~~shell
$ ls -lR | grep ^- | wc -l
~~~

统计文件夹下目录的个数，包括子文件夹里的:
~~~shell
ls -lR | grep ^d | wc -l
~~~

#### free

#### awk
