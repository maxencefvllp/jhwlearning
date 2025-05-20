## Linux文件基本属性

常使用以下两个命令来修改文件或目录的所属用户与权限：

- chown (change owner) ： 修改所属用户与组。
- chmod (change mode) ： 修改用户的权限。

可以使用 **ll** 或者 **ls –l** 命令来显示一个文件的属性以及文件所属的用户和组

![img](Linux.assets/file-llls22.jpg)

每个文件的属性由左边第一部分的 10 个字符来确定（如下图）。

![363003_1227493859FdXT](Linux.assets/363003_1227493859FdXT.png)

对于文件来说，它都有一个特定的所有者，也就是对该文件具有所有权的用户。

同时，在Linux系统中，用户是按组分类的，一个用户属于一个或多个组。

文件所有者以外的用户又可以分为文件所属组的同组用户和其他用户。

因此，Linux系统按文件所有者、文件所有者同组用户和其他用户来规定了不同的文件访问权限。

```
[root@www /]# ls -l
total 64
drwxr-xr-x 2 root  root  4096 Feb 15 14:46 cron
drwxr-xr-x 3 mysql mysql 4096 Apr 21  2014 mysql
……

```

在以上实例中，mysql 文件是一个目录文件，属主和属组都为 mysql，属主有可读、可写、可执行的权限；与属主同组的其他用户有可读和可执行的权限；其他用户也有可读和可执行的权限。

对于 root 用户来说，一般情况下，文件的权限对其不起作用。

### 命令

1. chgrp：更改文件属组
   **chgrp [-R] 属组名 文件名****
   -R：递归更改文件属组，就是在更改某个目录文件的属组时，如果加上 **-R** 的参数，那么该目录下的所有文件的属组都会更改。

2. chown：更改文件所有者（owner），也可以同时更改文件所属组。

   **chown [–R] 所有者 文件名**
   **chown [-R] 所有者:属组名 文件名**
   进入 /root 目录（~）将install.log的拥有者改为bin这个账号：

   ```
   [root@www ~] cd ~
   [root@www ~]# chown bin install.log
   [root@www ~]# ls -l
   -rw-r--r--  1 bin  users 68495 Jun 25 08:53 install.log
   ```

   将install.log的拥有者与群组改回为root：

   ```
   [root@www ~]# chown root:root install.log
   [root@www ~]# ls -l
   -rw-r--r--  1 root root 68495 Jun 25 08:53 install.log
   
   ```

3. chmod：更改文件9个属性
   Linux 文件的基本权限就有九个，分别是 **owner/group/others(拥有者/组/其他)** 三种身份各有自己的 **read/write/execute** 权限。
   文件的权限字符为： **-rwxrwxrwx**，可以使用数字来代表各个权限，各权限的分数对照表如下：r:4 w:2 x:1
   每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为： **-rwxrwx---** 分数则是：

   owner = rwx = 4+2+1 = 7

   group = rwx = 4+2+1 = 7

   others= --- = 0+0+0 = 0

    **chmod [-R] xyz 文件或目录**

   - **xyz** : 就是刚刚提到的数字类型的权限属性，为 **rwx** 属性数值的相加。
   - **-R** : 进行递归(recursive)的持续变更，以及连同次目录下的所有文件都会变更

```
#  touch test1    // 创建 test1 文件
# ls -al test1    // 查看 test1 默认权限
-rw-r--r-- 1 root root 0 Nov 15 10:32 test1
# chmod u=rwx,g=rx,o=r  test1    // 修改 test1 权限
# ls -al test1
-rwxr-xr-- 1 root root 0 Nov 15 10:32 test1

```

# 文件与目录管理

Linux 的目录结构为树状结构，最顶级的目录为根目录 **/**

## 处理目录命令

- ls（英文全拼：list files）: 列出目录及文件名
  -a ：全部的文件，连同隐藏文件( 开头为 . 的文件) 一起列出来(常用)
  -d ：仅列出目录本身，而不是列出目录内的文件数据(常用)
  -l ：长数据串列出，包含文件的属性与权限等等数据；(常用)

- cd（英文全拼：change directory）：切换目录

  ```
  #使用 mkdir 命令创建 runoob 目录
  [root@www ~]# mkdir runoob
  
  #使用绝对路径切换到 runoob 目录
  [root@www ~]# cd /root/runoob/
  
  #使用相对路径切换到 runoob 目录
  [root@www ~]# cd ./runoob/
  
  # 表示回到自己的家目录，亦即是 /root 这个目录
  [root@www runoob]# cd ~
  
  # 表示去到目前的上一级目录，亦即是 /root 的上一级目录的意思；
  [root@www ~]# cd ..
  
  ```

  

- pwd（

  ```
  [root@www ~]# pwd [-P]
  -P ：显示出确实的路径，而非使用链接 (link) 路径。
  
  [root@www ~]# cd /var/mail   <==注意，/var/mail是一个链接档
  [root@www mail]# pwd
  /var/mail         <==列出目前的工作目录
  [root@www mail]# pwd -P
  /var/spool/mail   <==怎么回事？有没有加 -P 差很多～
  [root@www mail]# ls -ld /var/mail
  lrwxrwxrwx 1 root root 10 Sep  4 17:54 /var/mail -> spool/mail
  # 看到这里应该知道为啥了吧？因为 /var/mail 是链接档，链接到 /var/spool/mail 
  # 所以，加上 pwd -P 的选项后，会不以链接档的数据显示，而是显示正确的完整路径啊！
  
  ```

  英文全拼：print work directory）：显示目前的目录
  

- mkdir（英文全拼：make directory）：创建一个新的目录

  ```
  mkdir [-mp] 目录名称
  -m ：配置文件的权限喔！直接配置，不需要看默认权限 (umask) 的脸色～
  [root@www tmp]# mkdir -m 711 test2
  [root@www tmp]# ls -l
  drwxr-xr-x  3 root  root 4096 Jul 18 12:50 test
  drwxr-xr-x  3 root  root 4096 Jul 18 12:53 test1
  drwx--x--x  2 root  root 4096 Jul 18 12:54 test2
  
  -p ：帮助你直接将所需要的目录(包含上一级目录)递归创建起来！
  [root@www ~]# cd /tmp
  [root@www tmp]# mkdir test    <==创建一名为 test 的新目录
  [root@www tmp]# mkdir test1/test2/test3/test4
  mkdir: cannot create directory `test1/test2/test3/test4': 
  No such file or directory       <== 没办法直接创建此目录啊！
  [root@www tmp]# mkdir -p test1/test2/test3/test4
  
  ```

  

- rmdir（英文全拼：remove directory）：删除一个空的目录

  ```
   rmdir [-p] 目录名称
  -p ：从该目录起，一次删除多级空目录
  ```

  

- cp（英文全拼：copy file）: 复制文件或目录

  ```
  [root@www ~]# cp [-adfilprsu] 来源档(source) 目标档(destination)
  [root@www ~]# cp [options] source1 source2 source3 .... directory
  -a：相当於 -pdr 的意思，至於 pdr 请参考下列说明；(常用)
  
  -d：若来源档为链接档的属性，则复制链接档属性而非文件本身；
  
  -f：为强制(force)的意思，若目标文件已经存在且无法开启，则移除后再尝试一次；
  
  -i：若目标档(destination)已经存在时，在覆盖时会先询问动作的进行(常用)
  
  -l：进行硬式链接(hard link)的链接档创建，而非复制文件本身；
  
  -p：连同文件的属性一起复制过去，而非使用默认属性(备份常用)；
  
  -r：递归持续复制，用於目录的复制行为；(常用)
  
  -s：复制成为符号链接档 (symbolic link)，亦即『捷径』文件；
  
  -u：若 destination 比 source 旧才升级 destination ！
  ```

  

- rm（英文全拼：remove）: 删除文件或目录

  ```
   rm [-fir] 文件或目录
  -f ：就是 force 的意思，忽略不存在的文件，不会出现警告信息；
  -i ：互动模式，在删除前会询问使用者是否动作
  -r ：递归删除啊！最常用在目录的删除了！这是非常危险的选项！！！
  ```

  

- mv（英文全拼：move file）: 移动文件与目录，或修改文件与目录的名称

  ​	

  ```
  [root@www ~]# mv [-fiu] source destination
  [root@www ~]# mv [options] source1 source2 source3 .... directory
  -f ：force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
  -i ：若目标文件 (destination) 已经存在时，就会询问是否覆盖！
  -u ：若目标文件已经存在，且 source 比较新，才会升级 (update)
  ```

  ## 文件内容查看

  - cat 由第一行开始显示文件内容

    ```
    cat [-AbEnTv]
    
    - -A ：相当於 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
    - -b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
    - -E ：将结尾的断行字节 $ 显示出来；
    - -n ：列印出行号，连同空白行也会有行号，与 -b 的选项不同；
    - -T ：将 [tab] 按键以 ^I 显示出来；
    - -v ：列出一些看不出来的特殊字符
    
    [root@www ~]# cat /etc/issue
    CentOS release 6.4 (Final)
    Kernel \r on an \m
    
    ```

    

  - tac 从最后一行开始显示，可以看出 tac 是 cat 的倒着写！

    ```
    [root@www ~]# tac /etc/issue
    
    Kernel \r on an \m
    CentOS release 6.4 (Final)
    
    ```

    

  - nl  显示的时候，顺道输出行号

    ```
    nl [-bnw] 文件
    -b ：指定行号指定的方式，主要有两种：
    -b a ：表示不论是否为空行，也同样列出行号(类似 cat -n)；
    -b t ：如果有空行，空的那一行不要列出行号(默认值)；
    -n ：列出行号表示的方法，主要有三种：
    -n ln ：行号在荧幕的最左方显示；
    -n rn ：行号在自己栏位的最右方显示，且不加 0 ；
    -n rz ：行号在自己栏位的最右方显示，且加 0 ；
    -w ：行号栏位的占用的位数。
    ```

    

  - more 一页一页的显示文件内容

    ```
    [root@www ~]# more /etc/man_db.config 
    #
    # Generated automatically from man.conf.in by the
    # configure script.
    #
    # man.conf from man-1.6d
    ....(中间省略)....
    --More--(28%)  <== 重点在这一行喔！你的光标也会在这里等待你的命令
    
    在 more 这个程序的运行过程中，你有几个按键可以按的：
    
    空白键 (space)：代表向下翻一页；
    Enter         ：代表向下翻『一行』；
    /字串         ：代表在这个显示的内容当中，向下搜寻『字串』这个关键字；
    :f            ：立刻显示出档名以及目前显示的行数；
    q             ：代表立刻离开 more ，不再显示该文件内容。
    b 或 [ctrl]-b ：代表往回翻页，不过这动作只对文件有用，对管线无用。
    ```

    

  - less 与 more 类似，但是比 more 更好的是，他可以往前翻页！

    ```
    空白键    ：向下翻动一页；
    [pagedown]：向下翻动一页；
    [pageup]  ：向上翻动一页；
    /字串     ：向下搜寻『字串』的功能；
    ?字串     ：向上搜寻『字串』的功能；
    n         ：重复前一个搜寻 (与 / 或 ? 有关！)
    N         ：反向的重复前一个搜寻 (与 / 或 ? 有关！)
    q         ：离开 less 这个程序；
    ```

    

  - head 只看头几行

    ```
    head [-n number] 文件 
    -n ：后面接数字，代表显示几行的意思
    
    [root@www ~]# head -n 20 /etc/man.config   #显示20行
    ```

    

  - tail 只看尾巴几行

    ```
    tail [-n number]-n ：后面接数字，代表显示几行的意思
    -f ：表示持续侦测后面所接的档名，要等到按下[ctrl]-c才会结束tail的侦测 文件 
    
    ```

    

# 用户和用户组管理

用户的账号一方面可以帮助系统管理员对使用系统的用户进行跟踪，并控制他们对系统资源的访问；另一方面也可以帮助用户组织文件，并为用户提供安全性保护。