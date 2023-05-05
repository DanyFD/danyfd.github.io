---
title: linux笔记
date: 2023-05-05 10:59:41
tags: "linux"
categories: "linux"
---

## 知识点

### 常用快捷键

`[Ctrl]-d`：可以用于取代exit的输入

### 虚拟机目录

![](https://s1.ax1x.com/2023/03/21/ppUSxX9.jpg)

#### 目录含义

`/bin`：存放经常使用的命令

`/home`：存放普通用户的主目录，一般该目录名是以用户的账号命名

`/root`：该目录为系统管理员，也称作超级权限者的用户主目录

`/boot`：Linux启动相关文件

`/lib`：系统开机所需最基本的动态连接共享库，作用类似Windows里的DLL文件

`/lost+found`：一般情况下是空的，当系统非法关机后存放了一些文件

`/etc`：系统管理所需配置和子文件目录

`/user`：用户应用程序和文件

`/proc`：虚拟目录，系统内存映射，访问这个目录获取系统信息

`/srv`：存放服务启动后所需数据

`/sys`：该目录安装了2.6内核新出现的文件系统

`/tmp`：存放临时文件

`/mnt`：存放挂载文件

`/opt`：给主机额外安装软件的目录，即软件存放目录

`/user/local`：软件安装后的目标目录，一般是编译源码的方式安装的程序

#### 颜色含义

蓝色表示目录，白色表示文件，红色表示压缩文件，绿色表示可执行权限

### Linux启动过程

可以分为5个阶段：

- 内核的引导
- 运行`init`
- 系统初始化
- 建立终端
- 用户登录系统

> init程序的类型：
>
> - **SysV:** init, CentOS 5之前, 配置文件： /etc/inittab
> - **Upstart:** init,CentOS 6, 配置文件： /etc/inittab, /etc/init/*.conf
> - **Systemd：** systemd, CentOS 7,配置文件： /usr/lib/systemd/system /etc/systemd/system

#### 内核引导

`BIOS`开机自检，按照设置的启动设备（通常是硬盘）启动，操作系统接管硬件后首先读入`/boot`下的内核文件

#### 运行init

init进程是所有进程的起点，init程序首先需要读取配置文件`/etc/inittab`

#### 运行级别

许多程序需要开机启动，在Windows叫做服务（service），在Linux就叫做守护进程（daemon）

init进程的一大任务即运行这些开机启动的程序，但是不同场合需启动不同程序，Linux允许为不同场合分配不同的开机启动程序（运行级别）

Linux系统有7个运行级别(runlevel)：

- 运行级别0：系统停机状态，系统默认级别不能设为0，否则不能正常启动
- 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登录
- 运行级别2：多用户状态(没有NFS，网络文件系统)
- 运行级别3：完全的多用户状态(有NFS)，登录后进入控制台命令行模式
- 运行级别4：系统未使用，保留
- 运行级别5：X11控制台，登录后进入图形GUI模式
- 运行级别6：系统正常关闭并重启，默认级别不能设为6，否则不能正常启动

#### 系统初始化

在init的配置文件中有这么一行：si::sysinit:/etc/rc.d/rc.sysinit　它调用执行了`/etc/rc.d/rc.sysinit`，而`rc.sysinit`是一个bash shell的脚本，`rc.sysinit`是每一个运行级别都要首先运行的重要脚本，它主要完成的工作有：激活交换分区、检查磁盘、加载硬件模块以及其它一些需要优先执行的任务

#### 建立终端

rc执行完毕后，返回init。这时基本系统环境已经设置好了，各种守护进程也已经启动了。init接下来会打开6个终端，以便用户登录系统。在inittab中的以下6行就是定义了6个终端：

```
1:2345:respawn:/sbin/mingetty tty1
2:2345:respawn:/sbin/mingetty tty2
3:2345:respawn:/sbin/mingetty tty3
4:2345:respawn:/sbin/mingetty tty4
5:2345:respawn:/sbin/mingetty tty5
6:2345:respawn:/sbin/mingetty tty6
```

2、3、4、5的运行级别中都将以respawn方式运行mingetty程序，mingetty程序能打开终端、设置模式

### Vim编辑器

#### 四种模式

##### 命令模式

使用Vim编辑器时默认处于命令模式，该模式下可以移动光标位置，可以通过快捷键对文件内容进行复制、粘贴、删除等操作

###### 如何进入命令模式

当使用vim命令直接打开某个文件时，默认进入的就是命令模式；若处于其它模式时连续按两次Esc键也可以返回命令模式

##### 编辑模式或输入模式

在命令模式下输入小写字母a或i即可进入编辑模式

##### 末行模式

可以在末行输入命令对文件进行查找、替换、保存、退出等操作

###### 如何进入末行模式

进入末行模式的方法只有一个，在命令模式下使用`:`或`/`的方式进入

##### 可视化模式

可以做一些列选操作（通过方向键选择某些列的内容，类似Windows鼠标刷黑）

###### 如何进入可视化模式

在命令模式中，按`ctrl+v（可视块）`或`ctrl+V（可视行）`或`v（可视）`，然后根据方向键选择需要复制的区块，按下`y`进行复制，最后按下`p`粘贴

退出可视模式按下`Esc`

##### 四种模式的关系

![](https://s1.ax1x.com/2023/02/03/pSsnXVS.png)

#### 各模式下的相关操作

##### 命令模式

###### 移动光标

向下移动30行 => 30↓

光标向右移动0n个字符 => n\<space>

移动到该行的最前面的字符处 => 0

移动到该行的最后面的字符处 => $

移动到这个屏幕的最上方那一行的第一个字符 => H

移动到这个屏幕的中央那一行的第一个字符 => M

移动到这个屏幕的最下方那一行的第一个字符 => L

移动到这个档案的第一行 => gg

移动到这个档案的最后一行 => G

移动光标到第150行 => 150G

光标向下移动n行 => n\<Enter>

###### 翻屏

向上翻屏：`ctrl + b（before）或 PgUp`

向下翻屏：`ctrl + f（after）或 PgDn`

向上翻半屏：`ctrl + u（up）`

向下翻半屏：`ctrl + d（down）`

###### 复制/粘贴

复制当前行（光标所在那一行）=> yy，如复制5行，5yy

粘贴：在想要粘贴的地方按下p【将粘贴光标所在行的下一行】，如果想粘贴光标所在行之前可以使用P键

###### 剪切/删除

在Vim中，剪切和删除都是`dd`，若未用p进行粘贴就是删除；粘贴了就是剪切

剪切/删除光标所在当前行之后的内容，但是删除之后下一行不上移按键：D（删除之后当前行会变成空白行）

###### 撤销/恢复

撤销：u（undo）

恢复：ctrl + r恢复（取消）之前的撤销操作【重做，redo】

##### 末行模式

###### 查找/搜索

在命令模式下输入`/`，进入末行模式后输入要查找或搜索的关键词

若在一个文件中存在多个满足条件的结果。在搜索结果中切换上/下一个结果：N/n（大写N代表上一个结果，小写n代表next）

若不需要高亮，则在末行模式输入`:noh`【no highlight】

###### 文件内容的替换

第一步：首先进入末行模式

第二步：根据需求替换内容

只替换光标所在行第一个满足的结果

```
:s/要替换的关键词/替换后的关键词	+	回车
```

替换光标所在行所有满足条件的结果（替换多次，只能替换一行）

```
:s/要替换的关键词/替换后的关键词/g	g=global全局替换
```

针对整个文档中的所有行进行替换，只替换每一行中满足条件的第一个结果

```
:%s/要替换的关键词/替换后的关键词
```

针对整个文档中的所有关键词进行替换（只要满足条件就进行替换操作）

```
:%s/要替换的关键词/替换后的关键词/g
```

###### 显示行号

```
:set nu
【nu = number】，行号
```

```
取消行号 => :set nonu
```

###### set paste模式

为什么要使用paste模式？

在终端Vim中粘贴代码时会发现插入的代码有多余的缩进，且逐行累加。原因是终端把粘贴的文本存入键盘缓存（Keyboard Buffer）中，Vim则把这些内容作为用户的键盘输入来处理。导致在遇到换行符的时候，如果Vim开启了自动缩进，就会默认的把上一行缩进插入到下一行的开头，最终使代码变乱。

在粘贴数据之前开启paste模式：`:set paste`

在粘贴完毕后关闭paste模式：`:set nopaste`

#### Vim实用功能

##### 代码着色

通过`:syntax on`或`:syntax off` 开启或关闭代码着色功能

##### 异常退出解决方案

突然关闭终端或断电下退出的情况为异常退出，文件一般为`.文件名称.swp`

解决办法：将交换文件直接删除

### 用户管理

```
添加用户：useradd 用户名
给用户指定密码：passwd 用户名
删除用户（保留家目录）：userdel 用户名
删除用户（删除所有目录）：userdel -r 用户名
查询用户信息：id 用户名
查看当前登录用户：who am i
```

### CentOS7找回root密码

1. 启动系统，进入开机页面，按`e`键进入编辑页面
2. 光标向下移动，找到“Linux16”开头的行数，行末输入`init=/bin/sh`，接着按`ctrl+x`进入单用户模式
3. 在光标闪烁位置输入：mount -o remount,rw /，完成后回车
4. 接着输入passwd，完成后回车，输入密码后回车，再次输入密码，修改成功后会显示passwd...
5. 接着在光标位置输入：touch / .autorelabel，完成后回车，等待系统重启，新密码生效

### 开关机问题

`sync`：将内存中尚未更新的数据写入硬盘中；目前的shutdown/reboot/halt等指令均在关机前进行了sync

`shutdown`：只有root拥有该权限

| 选项与参数 | 说明                                   |
| ---------- | -------------------------------------- |
| -t sec     | -t后面加秒数，即几秒后关机             |
| -k         | 不是真的关机，只是发送警告讯息         |
| -r         | 在系统服务停掉后重新启动（常用）       |
| -h         | 在系统服务停掉后立即关机               |
| -n         | 不经过init程序，直接以shutdown功能关机 |
| -f         | 关机并开机后，强制略过fsck的磁盘检查   |
| -c         | 取消已经在进行的shutdown指令内容       |

`忘记root密码`：

1. 启动虚拟机并按e进入下列页面

   ![](https://img-blog.csdnimg.cn/4426447226b8445b8df7c8e3ee63a1fb.png)

2. 用键盘上下移动光标，到linuix…UTF-8后面加上rd.break,按下Ctrl+x

   ![](https://img-blog.csdnimg.cn/f6ca6a8701094d31a4f55380a88f9b63.png)

3. 接下来进入switch_root页面里，输入

   \# mount -o remount,rw /sysroot

   \# chroot /sysroot

4. 之后就会进入sh-4.2#的页面输入passwd，输入你的新密码（直接输入就行了，不显示字符）

   ![](https://img-blog.csdnimg.cn/181e7534a8804eb4b9acb5fdf0897941.png)

5. 输入touch / .autorelabel

   输入exit回到switch_root页面

   再次输入exit

### 文件属性

#### 查看文件属性

`ls [-al]`显示文件名及相关属性

![](https://s1.ax1x.com/2023/02/03/pSsnqDf.png)

![](https://s1.ax1x.com/2023/02/03/pSsn7vt.png)

- 第一个字符代表文件类型[目录、档案或链接文件等]
  - 当为[d]则是目录
  - 当为[-]则是档案
  - 若为[|]则表示为连结档（link file）
  - 若是[b]则表示为装置文件内的可供储存的接口设备（可随机存取装置）
  - 若是[c]则表示为装置文件内的串行端口设备，例如键盘、鼠标（一次性读取装置）

接下来的字符中，以三个为一组，且均为 **rwx** 的三个参数的组合，若无权限则以`-`表示

#### 更改文件属性

##### chgrp：更改文件属组

`chgrp [-R] 属组名 文件名`

- `-R`：递归更改文件属组，若加上该参数，目录下的所有文件的属组都会更改

##### chown：更改文件属主或属组

```
chown [-R] 属主名 文件名
chown [-R] 属主名:属组名 文件名


```

##### chmod：更改文件的9个属性

Linux 文件的基本权限就有九个，分别是 owner/group/others(拥有者/组/其他) 三种身份各有自己的 read/write/execute权限

其中字符权限对应的数字为：`r:4 w:2 x:1`

故当权限为`-rwxrwx---`时对应的权限为`owner:7 group:7 others:0`

###### 数字修改权限

`chmod [-R] xyz 文件或目录`

- **xyz** : 即数字类型的权限属性，为 **rwx** 属性数值的相加
- **-R** : 进行递归(recursive)的持续变更，以及连同次目录下的所有文件都会变更

###### 字符修改权限

| chmod | u g o a | +(加入) -(除去) =(设定) | r w x | 文件或目录 |
| ----- | ------- | ----------------------- | ----- | ---------- |
|       |         |                         |       |            |

`chmod u=rwx,g=rx,o=r 文件名`

若要去掉全部人的可执行权限，可以：`chmod a-x`

### 磁盘管理

Linux 磁盘管理常用三个命令为 **df**、**du** 和 **fdisk**：

- `df`：列出文件系统的整体磁盘使用量，检查文件系统的磁盘空间占用情况
- `du`：检查磁盘空间使用量，是对文件和目录磁盘使用的空间查看
- `fdisk`：用于磁盘分区

#### df

`df [-ahikHTm] [目录或文件名]`

- -a ：列出所有的文件系统，包括系统特有的 /proc 等文件系统
- -k ：以 KBytes 的容量显示各文件系统
- -m ：以 MBytes 的容量显示各文件系统
- -h ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示
- -H ：以 M=1000K 取代 M=1024K 的进位方式
- -T ：显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出
- -i ：不用硬盘容量，而以 inode 的数量来显示

#### du

`du [-ahskm] 文件或目录名称`

- -a ：列出所有的文件与目录容量，因为默认仅统计目录底下的文件量而已
- -h ：以人们较易读的容量格式 (G/M) 显示
- -s ：列出总量而已，而不列出每个各别的目录占用容量
- -S ：不包括子目录下的总计，与 -s 有点差别
- -k ：以 KBytes 列出容量显示
- -m ：以 MBytes 列出容量显示

#### fdisk

`fdisk [-l] 装置名称`

- `-l`：输出后面接的装置所有的分区内容，仅为`fdisk -l`时会将整个系统的装置分区均列出来

#### mkfs：磁盘格式化

`mkfs [-t 文件系统格式] 装置文件名`

- -t ：可以接文件系统格式，例如 ext3, ext2, vfat 等(系统有支持才会生效)

###### 查看mkfs支持的文件格式

```
[root@www ~]# mkfs[tab][tab]
mkfs         mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.msdos   mkfs.vfat


```

###### 将分区 /dev/hdc6格式化为 ext3 文件系统

```
[root@www ~]# mkfs -t ext3 /dev/hdc6
mke2fs 1.39 (29-May-2006)
Filesystem label=                <==这里指的是分割槽的名称(label)
OS type: Linux
Block size=4096 (log=2)          <==block 的大小配置为 4K 
Fragment size=4096 (log=2)
251392 inodes, 502023 blocks     <==由此配置决定的inode/block数量
25101 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=515899392
16 block groups
32768 blocks per group, 32768 fragments per group
15712 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912

Writing inode tables: done
Creating journal (8192 blocks): done <==有日志记录
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 34 mounts or
180 days, whichever comes first.  Use tune2fs -c or -i to override.
# 这样就创建起来我们所需要的 Ext3 文件系统了！简单明了！


```

#### fsck：磁盘检验

用于检查和维护不一致的文件系统，若系统掉电或磁盘发生问题，可用其对文件系统进行检查

`fsck [-t 文件系统] [-ACay] 装置名称`

- -t : 给定档案系统的型式，若在 /etc/fstab 中已有定义或 kernel 本身已支持的则不需加上此参数
- -s : 依序一个一个地执行 fsck 的指令来检查
- -A : 对/etc/fstab 中所有列出来的 分区（partition）做检查
- -C : 显示完整的检查进度
- -d : 打印出 e2fsck 的 debug 结果
- -p : 同时有 -A 条件时，同时有多个 fsck 的检查一起执行
- -R : 同时有 -A 条件时，省略 / 不检查
- -V : 详细显示模式
- -a : 如果检查有错则自动修复
- -r : 如果检查有错则由使用者回答是否修复
- -y : 选项指定检测每个文件是自动输入yes，在不确定那些是不正常的时候，可以执行 # fsck -y 全部检查修复。

#### mount、umount

Linux 的磁盘挂载使用 `mount` 命令，卸载使用 `umount` 命令

`mount [-t 文件系统] [-L Label名] [-o 额外选项] [-n]  装置文件名  挂载点`

`umount [-fn] 装置文件名或挂载点`

- -f ：强制卸除！可用在类似网络文件系统 (NFS) 无法读取到的情况下
- -n ：不升级 /etc/mtab 情况下卸除

### 定时任务调度

任务调度：系统在某个时间执行特定的命令或程序

#### 任务调度分类

1. 系统工作：某些重要工作周而复始的进行，如病毒查杀

2. 个别用户工作：个别用户执行某些程序，如打开qq

   **定时任务的设置：crontab [选项]**

| 选项 | 功能                       |
| ---- | -------------------------- |
| -e   | 编辑crontab定时任务        |
| -l   | 查询当前任务调度           |
| -r   | 删除当前用户所有的定时任务 |

#### 重启任务调度

service crond restart

crontab -e 回车然后输入 */1 * * * * ls

| *号位置 | 含义                                         |
| ------- | -------------------------------------------- |
| 第一个  | 一小时当中的第几分钟（分钟）                 |
| 第二个  | 一天当中的第几小时（小时）                   |
| 第三个  | 一月当中的第几天（天）                       |
| 第四个  | 一年中第几个月（月）                         |
| 第五个  | 一周当中的星期几（范围0-7,0和7都表示星期天） |

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 表示任何时间，比如第一个*表示一小时每分钟都执行一次          |
| ,        | 表示不连续时间，比如"0 8,10 * * *"表示每天8点和10点执行      |
| -        | 表示连续的时间范围，比如"0 2 * * 1-6"表示周一到周六凌晨2点执行一次命令 |
| */n      | 表示每隔多久执行一次，如"*/10 * * * *"表示每10分钟执行一次   |

**特定时间执行案例**

![](https://s1.ax1x.com/2023/02/03/pSsnLb8.png)

#### at定时任务

at命令是一次性定时计划任务 ，at的守护进程atd会以后台模式检查作业队列运行。默认情况下, atd守护进程每60秒检查作业队列,有作业时,会检查作业运行时间,如果时间与当前时间四配，则运行此作业。at命令只执行一次。

在使用at命令的时候，一定要保证atd进程的启动，可以使用相关指令来查看

检测当前进程有哪些：ps -ef

检测acd进程是否在运行：ps -ef | grep atd

命令格式：at 【选项】 【时间】，ctrl+d结束at命令输入

| 选项        | 功能                                                   |
| ----------- | ------------------------------------------------------ |
| -m          | 当指定的任务被完成后,将给用户发送邮件,即使没有标准输出 |
| -I          | atq（显示系统中待执行的任务列表）的别名                |
| -d          | atrm（删除待执行任务队列中的任务）的别名               |
| -v          | 显示任务将被执行的时间                                 |
| -V          | 显示版本信息                                           |
| -c          | 打印任务的内容到标准输出                               |
| -q 队列     | 使用指定的队列                                         |
| -f 文件     | 从指定文件读入任务而不是从标准输入读入                 |
| -t 时间参数 | 以时间参数的形式提交要运行的任务                       |

##### at指定时间方式

1. hh:mm（小时：分钟）24小时制指定时间，若该时间已过，会到第二天执行
2. 使用midnight (深夜)， noon (中午)， teatime (饮茶时间，一般是下午4点)等模糊词来指定时间
3. 采用12小时计时制，在后面加上am (上午)或pm (下午)说明是上午还是下午
4. 指定命令执行的具体日期，指定格式为month day(月日)或mm/dd/yy (月/日/年)或dd.mm.yy
5. 使用相对计时法，如：now + 5 minutes
6. 直接使用today、tomorrow指定完成命令的时间

###### 使用示例

一天后凌晨12点执行 /bin/ls home

![](https://img-blog.csdnimg.cn/20210717194539556.png)

- 统计/opt下文件个数：`ls -l /opt | grep "^-" | wc -l`（^-是以-开头的文件，wc统计个数）
- 统计/opt下目录个数：`ls -l /opt | grep "^d" | wc -l`
- 统计/opt文件夹下文件的个数，包括子文件夹里的：`ls -lR /opt | grep "^-" | wc -l `
- 统计/opt文件夹下目录的个数，包括子文件夹里的：`ls -lR /opt | grep "^d" | wc -l`
- 以树状显示目录结构：`tree 目录`，注意默认是没有安装tree的，安装要root权限，安装tree命令：`yum install tree`

### 进程管理

使用`ps 【选项】`查看当前系统哪些进程在执行

| 选项 | 功能                       |
| ---- | -------------------------- |
| -a   | 显示当前终端的所有进程信息 |
| -u   | 以用户的格式显示进程信息   |
| -x   | 显示后天进程运行的参数     |
| -e   | 显示所有进程               |
| -f   | 全格式                     |

#### 终止进程

kill 【选项】 进程号

killall 进程名称

常用选项：-9：强迫进程立即停止

#### 查看进程树

pstree 【选项】

常用选项

-p：显示进程的PID

-u：显示进程的所属用户

#### 服务管理

service 服务名 [start|stop|restart|reload|status]

在CentOS7后很多服务不再使用service，而是systemctl

查看service指令管理的服务：ls -l /etc/init.d

#### chkconfig指令

用于检查、设置系统的各种服务

查看服务：chkconfig --list [| grep xxx]

给服务在指定运行级别下设置开关：chkconfig --level 5 服务名 on/off

#### systemctl指令

管理操作系统和服务的命令，是systemd（system daemon，操作系统服务管理器）交互的主要工具，实现的功能包含了service和chkconfig这两个命令的功能

```
#例：查看当前防火墙的状况，关闭防火墙和启动防火墙
1、systemctl status firewalld.service
2、systemctl stop firewalld.service
3、systemctl start firewalld.service


```

#### 防火墙

![](https://s1.ax1x.com/2023/02/03/pSsnbKP.png)

防火墙开启时xshell访问linux需要打开22端口号，关闭后可以直接访问

#### firewall指令

打开端口：firewall-cmd --permanent --add-port=端口号/协议

关闭端口：firewall-cmd --permanent --remove-port=端口号/协议

重新载入才能生效：firewall-cmd --reload

查看所有开放端口：firewall-cmd --zone=public --list-port

查询端口是否开放：firewall-cmd --query-port=端口/协议

#### 动态监控进程

top与ps命令相似，用于显示正在执行的进程，top和ps最大的不同之处在于top在执行一段时间可以更新正在运行的进程

指令：top	【选项】

| 选项    | 功能                                       |
| ------- | ------------------------------------------ |
| -d 秒速 | 指定top命令每隔几秒更新，默认3秒           |
| -i      | 使top不显示任何闲置或僵死进程              |
| -P      | 通过指定监控进程ID来仅仅监控某个进程的状态 |

交互操作说明

| 操作 | 功能                                    |
| ---- | --------------------------------------- |
| P    | 以CPU使用率排序，从大到小，默认就是此项 |
| M    | 以内存的使用率排序，从大到小            |
| N    | 以PID排序，从大到小                     |
| Q或q | 退出top                                 |

### rpm和yum

#### rpm

用于下载包的打包及安装工具，它生成具有.rpm扩展名的文件，类似windows的setup.exe；

```
查询所有安装rpm软件包：rpm -qa
查询软件包是否安装：rpm -q 软件包名
查询软件包信息：rpm -qi 软件包名
查询软件包中的文件：rpm -ql 软件包名
查询文件所属的软件包：rpm -qf 文件全路径名
卸载软件包：rpm -e 软件包
安装软件包：rpm -ivh 安装的全路径


```

#### yum

Shell前端软件包管理器，基于RPM包管理，能够从指定的服务器自动下载RPM包并安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包

查询yum服务器是否有需要安装的软件：yum list | grep xx软件列表

安装指定的yum包：yum install xx下载安装

### Java环境安装

#### jdk安装

##### 安装步骤

1. 创建jdk文件夹：mkdir /opt/jdk

2. 通过xftp传输Linux版本的jdk安装包到/opt/jdk目录下

3. 进入jdk目录：cd /opt/jdk

4. 解压jdk安装包：tar -zxvf jdk-8u261-linux-x64.tar.gz

5. 创建java文件夹：mkdir /usr/local/java

6. 移动jdk安装文件：mv /opt/jdk/jdk 1.8.0_261/ /usr/local/java

7. 配置环境变量：vim /etc/profile

8. 在profile文末添加

   ```
   export JAVA_HOME=/usr/local/java/jdk1.8.0_261
   export PATH=$JAVA_HOME/bin:$PATH
   
   
   ```

9. 让编辑过的环境变量生效：source /etc/profile

#### Tomcat安装

##### 安装步骤

1. 新建tomcat目录：mkdir /opt/tomcat
2. 通过xftp传输Linux版本的tomcat安装包到/opt/tomcat目录下
3. 进入tomcat目录：cd /opt/tomcat
4. 解压tomcat：tar -zxvf apache-tomcat-8.5.69.tar.gz ，下载core核心包地址https://tomcat.apache.org/download-80.cgi
5. 进入tomcat的bin目录：cd apache-tomcat-8.5.69/bin，启动tomcat：./startup.sh
6. 开放端口8080：firewall-cmd --permanent --add-port=8080/tcp
7. 重新载入生效：firewall-cmd --reload
8. 测试是否打开端口号：firewall-cmd --query-port=8080/tcp

#### MySQL安装

##### 安装步骤

1. 新建mysql文件夹，进入：mkdir /opt/mysql

2. Xftp将安装包传输到/opt/tomcat目录下

3. 进入mysql目录：cd /opt/mysql

4. 解压mysql安装包：tar -xvf mysql-5.7.26-1.el7.x86_64.rpm-bundle.tar

5. 查询mariadb： rpm -qa | grep mari。注意centos7.6自带的类mysql数据库是mariadb，会跟mysql冲突，要先删除

6. 卸载mariadb：rpm -e --nodeps mariadb-libs ，rpm -e --nodeps marisa

7. 开始安装mysql

   ```
   rpm -ivh mysql-community-common-5.7.26-1.el7.x86_64.rpm 
   rpm -ivh mysql-community-libs-5.7.26-1.el7.x86_64.rpm 
   rpm -ivh mysql-community-client-5.7.26-1.el7.x86_64.rpm
   rpm -ivh mysql-community-server-5.7.26-1.el7.x86_64.rpm 
   
   
   ```

8. 启动服务：systemctl start mysqld.service

9. 开始设置root密码；Mysql自动给root用户设置随机密码，运行grep “password” /var/log/mysqld.log可看到当前密码

   运行mysql -u root -p，复制粘贴输入上述密码

   修改密码

   运行如下命令使密码生效

   ```
   flush privileges;
   
   
   ```

### Shell编程

#### Shell脚本执行方式

##### 脚本格式要求

脚本以#!/bin/bash开头，需要可执行权限

##### 常用执行方式

1. 输入脚本的绝对路径或相对路径（./xxx.sh）
2. sh+脚本

#### shell变量

1. 定义变量：变量名=值
2. 撤销变量：unset 变量
3. 声明静态变量：readonly 变量，静态变量不能unset
4. 显示当前shell中所有变量：set
5. 一般情况下var和{var}没有区别，但是用${}会较精确的界定变量名称的范围

```
A=`date`	#运行反引号内的命令，将结果返回给变量A
A=$(date)	#等同于上面的语句


```

##### 设置环境变量（全局变量）

export 变量名=变量值（将shell变量输出为环境变量）

source 配置文件（让修改后的配置信息立即生效）

echo $变量名（查询环境变量的值）

###### shell注释

- 单行注释：通过一个'#'实现，开始部分的#不是用于注释的

- 多行注释

  ```
  <<xxxx
  	注释内容
  xxxx
  
  
  ```

  xxxx可以为任意字符串，中间部分为注释

##### 位置参数变量

用于在执行脚本时获取命令行的参数信息

```
$n	#n为数字，表示第几个参数，10以上需要用大括号如${10}
$*	#命令行所有参数，将所有参数当做一个整体
$@	#命令行所有参数，将每个参数分别输出
$#	#命令行中所有参数的个数


```

##### 预定义变量

事先定义好的变量，直接在脚本中使用

```
$$	#当前进程的进程号PID
$!	#后台运行的最后一个进程的进程号PID
$?	#最后一次执行命令的返回状态；若为0则表示正确执行


```

#### 运算符

```
$((表达式))或$[表达式]或expr m+n


```

#### 条件判断

```
if [ 条件判断 ]
then
代码
fi

#若输入参数大于等于60则输出及格了，若小于60则输出不及格
if [ $1 -ge 60 ]
then
	echo	"及格了"
elif [ $1 -lt 60 ]
then
	echo	"不及格"
fi


case $1 in
"1")
	echo	"周一"
;;
"2")
	echo	"周二"
;;
*)
	echo	"other"
;;
esac


for i in "$*"
do
	echo	"num is $i"
done

SUM=0
i=0
while [ $i -le $1]
do
	SUM=$[$SUM+$i]
	i=$[$i+1]
done
echo	"结果=$SUM"


```

#### read读取控制台输入

##### 基本语法

read [选项] [参数]

##### 选项

-p：指定读取值时的提示符；

-t：指定读取值时等待的时间；

##### 参数

变量：指定读取值的变量名

```
#!/bin/bash
#案例1：读取控制台输入一个num1值
read -p "请输入指定的num1=" NUM1
echo "输入的num1=$NUM1"
#案例2：读取控制台输入一个num2值，在5秒内输入
read -t 5 -p "请输入num2=" NUM2
echo "输入的num2=$NUM2"


```

#### 函数

##### basename

返回一个字符串参数的基本文件名称

###### 语法

basename String [ Suffix ]

###### 描述

读取String参数，删除以/结尾的前缀及任何指定的Suffix参数，并将剩余基本文件名称写至标准输出

1. 若String包含的都是斜杠字符，则将字符串更改为单个/

2. 若指定Suffix参数和剩余字符相同，则不修改此字符

   ```
   basename /u/dee/desktop/cns.boo cns.boo		=>	cns.boo
   
   
   ```

3. 若只与字符串的后缀相同，则除去指定后缀

   ```
   basename /u/dee/desktop/cns.boo .boo		=>	cns
   
   
   ```

###### 常见应用

1. 显示一个shell变量的基本名称

   ```
   basename $WORKFILE
   #显示WORKFILE值的基本名称，若WORKFILE变量值是/home/jim/program.c文件，则此命令显示program.c 
   
   
   ```

2. 构造一个和另一个文件名称相同（除了后缀）的文件名称

   ```
   OFILE=`basename $1 .c`.o
   
   
   ```

##### dirname

获取某个文件或目录的上一级目录

###### 应用实例

```
dirname /home/users/aoquan/
#输出结果：/home/users


```

##### 自定义函数

###### 应用实例

```
#!/bin/bash
#案例：计算输入两个参数的和，getSum
function getSum(){
        sum=$[$n1+$n2]
        echo "和是=$sum"
}
#输入两个值
read -p "第一个值=" n1
read -p "第二个值=" n2
#调用函数
getSum $n1 $n2


```

#### 数据备份案例

###### 需求分析

每天凌晨2:30备份数据库smile到/data/backup/db；备份开始和结束给出相应的提示信息；备份文件以备份时间为文件名，并打包成.tar.gz形式；在备份的同时检查是否有7天前备份的数据库文件，若有就将其删除

`vim /usr/sbin/mysql_db_backup.sh 内容如下`

```shell
#!/bin/bash
#备份目录
BACKUP=/data/backup/db
#当前时间
DATATIME=$(date + %Y-%m-%d_%H%M%S)
#数据库地址
HOST=localhost
#数据库用户名
DB_USER=root
#数据库密码
DB_PW=root
#备份的数据库名
DATABASE=smile
#若备份目录不存在则创建
[ ! -d "${BACKUP}/${DATATIME}" ] && mkdir -p "${BACKUP}/${DATATIME}"
#备份数据库
echo "开始备份数据库${DATABASE}"
mysqldump -u${DB_USER} -p${DB_PW} --host=${HOST} -q -R --databases ${DATABASE} | gzip > ${BACKUP}/${DATATIME}/$DATATIME.sql.gz
#将备份文件夹处理成.tar.gz的格式
cd ${BACKUP}
tar -zcvf $DATATIME.tar.gz ${DATATIME}
#删除对应的备份目录
rm -rf ${BACKUP}/${DATATIME}
#删除7天前的备份
find ${BACKUP} -atime +7 -name "*.tar.gz" -exec rm -rf {} \;
echo "备份数据库${DATABASE}成功"


```

`定时脚本`

`命令行敲crontab -e ，接着输入如下内容`

```
30 2 * * * /usr/sbin/mysql_db_backup.sh


```

### 日志管理

#### 系统常用日志

| 日志文件                                 | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| <font color=red>/var/log/boot.log</font> | 系统启动日志                                                 |
| <font color=red>/var/log/cron</font>     | 记录与系统定时任务相关的日志                                 |
| /var/log/cups/                           | 记录打印信息的日志                                           |
| /var/log/dmesg                           | 记录系统开机时自检信息，可以使用dmesg命令查看内核自检信息    |
| /var/log/btmp                            | 记录错误登录的日志，该日志为二进制文件，不能用vi查看，要使用lastb命令查看 |
| <font color=red>/var/log/lasllog</font>  | 记录系统所有用户最后一次登录时间的日志，也只能使用lastlog命令查看 |
| <font color=red>/var/log/mailog</font>   | 记录邮件信息的日志                                           |
| <font color=red>/var/log/message</font>  | 记录系统重要消息的日志，若系统出现问题，首先应该检查的就是该日志文件 |
| <font color=red>/var/log/secure</font>   | 记录验证和授权方面的信息，只要涉及账户和密码的程序都会记录，如系统的登录、ssh的登录、sudo授权 |
| /var/log/wtmp                            | 永久记录所有用户的登录、注销信息、同时记录系统的启动、重启、关机事件，只能使用lastb命令查看 |
| <font color=red>/var/log/ulmp</font>     | 记录当前已登录用户的信息，随着用户的登录和注销不断变化，使用w、who、users等命令查看 |