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

选项：
~~~shell

~~~

查看用户自己的进程:
~~~shell
# ps -l
~~~


查看系统所有进程包含系统其它使用者，较详细的信息：
~~~shell
# ps -aux
~~~

查看特定的进程：
~~~shell
# ps -aux | grep processX
~~~

