### 基本命令

![img](https://images2015.cnblogs.com/blog/31127/201705/31127-20170530141401383-1329040140.png)

* ###### 1. cd命令

  （它用于切换当前目录，它的参数是要切换到的目录的路径，可以是绝对路径，也可以是相对路径）

  - cd /home   进入 '/ home' 目录
  - cd ..       返回上一级目录
  - cd ../..     返回上两级目录
  - cd        进入个人的主目录
  - cd ~user1  进入个人的主目录
  - cd -       返回上次所在的目录

  ###### 2. pwd命令

  pwd 显示工作路径

  ###### 3. ls命令

  （查看文件与目录的命令，list之意）

  - ls 查看目录中的文件
  - ls -l 显示文件和目录的详细资料
  - ls -a 列出全部文件，包含隐藏文件
  - ls -R 连同子目录的内容一起列出（递归列出），等于该目录下的所有文件都会显示出来
  - ls [0-9] 显示包含数字的文件名和目录名

  ###### 4. cp 命令

  （用于复制文件，copy之意，它还可以把多个文件一次性地复制到一个目录下）

  - -a ：将文件的特性一起复制
  - -p ：连同文件的属性一起复制，而非使用默认方式，与-a相似，常用于备份
  - -i ：若目标文件已经存在时，在覆盖时会先询问操作的进行
  - -r ：递归持续复制，用于目录的复制行为
  - -u ：目标文件与源文件有差异时才会复制

  ###### 5.  mv命令

  （用于移动文件、目录或更名，move之意）

  - -f ：force强制的意思，如果目标文件已经存在，不会询问而直接覆盖
  - -i ：若目标文件已经存在，就会询问是否覆盖
  - -u ：若目标文件已经存在，且比目标文件新，才会更新

  ###### 6.  rm 命令

  （用于删除文件或目录，remove之意）

  - -f ：就是force的意思，忽略不存在的文件，不会出现警告消息
  - -i ：互动模式，在删除前会询问用户是否操作
  - -r ：递归删除，最常用于目录删除，它是一个非常危险的参数

  ###### 二、查看文件内容

  ###### 7. cat命令

  （用于查看文本文件的内容，后接要查看的文件名，通常可用管道与more和less一起使用）

  - `cat file1` 从第一个字节开始正向查看文件的内容
  - `tac file1` 从最后一行开始反向查看一个文件的内容
  - `cat -n file1` 标示文件的行数
  - `more file1` 查看一个长文件的内容
  - `head -n 2 file1` 查看一个文件的前两行
  - `tail -n 2 file1` 查看一个文件的最后两行
  - `tail -n +1000 file1` 从1000行开始显示，显示1000行以后的
  - `cat filename | head -n 3000 | tail -n +1000` 显示1000行到3000行
  - `cat filename | tail -n +3000 | head -n 1000` 从第3000行开始，显示1000(即显示3000~3999行)

  ###### 三、文件搜索

  ###### 8. find命令（）

  - `find / -name file1` 从 '/' 开始进入根文件系统搜索文件和目录
  - `find / -user user1` 搜索属于用户 'user1' 的文件和目录
  - `find /usr/bin -type f -atime +100` 搜索在过去100天内未被使用过的执行文件
  - `find /usr/bin -type f -mtime -10` 搜索在10天内被创建或者修改过的文件
  - `whereis halt` 显示一个二进制文件、源码或man的位置
  - `which halt` 显示一个二进制文件或可执行文件的完整路径

  删除大于50M的文件：

  ```
  find /var/mail/ -size +50M -exec rm {} ＼;
  ```

  ###### 四、文件的权限 - 使用 "+" 设置权限，使用 "-" 用于取消

  ###### 9. chmod 命令

  - ls -lh 显示权限
  - chmod ugo+rwx directory1 设置目录的所有人(u)、群组(g)以及其他人(o)以读（r，4 ）、写(w，2)和执行(x，1)的权限
  - chmod go-rwx directory1  删除群组(g)与其他人(o)对目录的读写执行权限

  ###### 10. chown 命令

  （改变文件的所有者）

  - chown user1 file1 改变一个文件的所有人属性
  - chown -R user1 directory1 改变一个目录的所有人属性并同时改变改目录下所有文件的属性
  - chown user1:group1 file1 改变一个文件的所有人和群组属性

  ###### 11. chgrp 命令

  （改变文件所属用户组）

  - chgrp group1 file1 改变文件的群组

  ###### 五、文本处理

  ###### 12. grep 命令

  （分析一行的信息，若当中有我们所需要的信息，就将该行显示出来，该命令通常与管道命令一起使用，用于对一些命令的输出进行筛选加工等等）

  - `grep Aug /var/log/messages` 在文件 '/var/log/messages'中查找关键词"Aug"
  - `grep ^Aug /var/log/messages` 在文件 '/var/log/messages'中查找以"Aug"开始的词汇
  - `grep [0-9] /var/log/messages` 选择 '/var/log/messages' 文件中所有包含数字的行
  - `grep Aug -R /var/log/*` 在目录 '/var/log' 及随后的目录中搜索字符串"Aug"
  - `sed 's/stringa1/stringa2/g' example.txt` 将example.txt文件中的 "string1" 替换成 "string2"
  - `sed '/^$/d' example.txt` 从example.txt文件中删除所有空白行

  ###### 13. paste 命令

  - `paste file1 file2` 合并两个文件或两栏的内容
  - `paste -d '+' file1 file2` 合并两个文件或两栏的内容，中间用"+"区分

  ###### 14. sort 命令

  - `sort file1 file2` 排序两个文件的内容
  - `sort file1 file2 | uniq` 取出两个文件的并集(重复的行只保留一份)
  - `sort file1 file2 | uniq -u` 删除交集，留下其他的行
  - `sort file1 file2 | uniq -d` 取出两个文件的交集(只留下同时存在于两个文件中的文件)

  ###### 15. comm 命令

  - `comm -1 file1 file2` 比较两个文件的内容只删除 'file1' 所包含的内容
  - `comm -2 file1 file2` 比较两个文件的内容只删除 'file2' 所包含的内容
  - `comm -3 file1 file2` 比较两个文件的内容只删除两个文件共有的部分

  ##### 六、打包和压缩文件

  ###### 16. tar 命令

  （对文件进行打包，默认情况并不会压缩，如果指定了相应的参数，它还会调用相应的压缩程序（如gzip和bzip等）进行压缩和解压）推荐：[250期面试题汇总](http://mp.weixin.qq.com/s?__biz=MzIyNDU2ODA4OQ==&mid=2247489003&idx=1&sn=69bf19d900079e204e36df58525654bf&chksm=e80da39ddf7a2a8bf0765f9b95f359a3944fc40c4a192bb3fe9adedfbcd0070cd27234bcf6b3&scene=21#wechat_redirect)

  - -c ：新建打包文件
  - -t ：查看打包文件的内容含有哪些文件名
  - -x ：解打包或解压缩的功能，可以搭配-C（大写）指定解压的目录，注意-c,-t,-x不能同时出现在同一条命令中
  - -j ：通过bzip2的支持进行压缩/解压缩
  - -z ：通过gzip的支持进行压缩/解压缩
  - -v ：在压缩/解压缩过程中，将正在处理的文件名显示出来
  - -f filename ：filename为要处理的文件
  - -C dir ：指定压缩/解压缩的目录dir
  - 压缩：`tar -jcv -f filename.tar.bz2` 要被处理的文件或目录名称
  - 查询：`tar -jtv -f filename.tar.bz2`
  - 解压：`tar -jxv -f filename.tar.bz2 -C` 欲解压缩的目录
  - `bunzip2 file1.bz2` 解压一个叫做 'file1.bz2'的文件
  - `bzip2 file1` 压缩一个叫做 'file1' 的文件
  - `gunzip file1.gz` 解压一个叫做 'file1.gz'的文件
  - `gzip file1` 压缩一个叫做 'file1'的文件
  - `gzip -9 file1` 最大程度压缩
  - `rar a file1.rar test_file` 创建一个叫做 'file1.rar' 的包
  - `rar a file1.rar file1 file2 dir1` 同时压缩 'file1', 'file2' 以及目录 'dir1'
  - `rar x file1.rar` 解压rar包
  - `zip file1.zip file1` 创建一个zip格式的压缩包
  - `unzip file1.zip` 解压一个zip格式压缩包
  - `zip -r file1.zip file1 file2 dir1` 将几个文件和目录同时压缩成一个zip格式的压缩包

  ##### 七、系统和关机 (系统的关机、重启以及登出 )

  - shutdown -h now 关闭系统(1)
  - init 0 关闭系统(2)
  - telinit 0 关闭系统(3)
  - shutdown -h hours:minutes & 按预定时间关闭系统
  - shutdown -c 取消按预定时间关闭系统
  - shutdown -r now 重启(1)
  - reboot 重启(2)
  - logout 注销
  - time 测算一个命令（即程序）的执行时间

  ###### 八、进程相关的命令

  ##### 17 jps命令

  （显示当前系统的java进程情况，及其id号）

  - jps(Java Virtual Machine Process Status Tool)是JDK 1.5提供的一个显示当前所有java进程pid的命令，简单实用，非常适合在linux/unix平台上简单察看当前java进程的一些简单情况。

  ##### 18 ps命令

  （用于将某个时间点的进程运行情况选取下来并输出，process之意）

  - -A ：所有的进程均显示出来
  - -a ：不与terminal有关的所有进程
  - -u ：有效用户的相关进程
  - -x ：一般与a参数一起使用，可列出较完整的信息
  - -l ：较长，较详细地将PID的信息列出

  ```
  ps aux # 查看系统所有的进程数据
  ps ax # 查看不与terminal有关的所有进程
  ps -lA # 查看系统所有的进程数据
  ps axjf # 查看连同一部分进程树状态
  ```

  ###### 19 kill命令

  （用于向某个工作（%jobnumber）或者是某个PID（数字）传送一个信号，它通常与ps和jobs命令一起使用）

  ###### 20 killall命令

  （向一个命令启动的进程发送一个信号）

  ###### 21 top命令

  是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况，类似于Windows的任务管理器。

  如何杀死进程：

  - 图形化界面的方式
  - kill -9 pid  （-9表示强制关闭）
  - killall -9 程序的名字
  - pkill 程序的名字

  查看进程端口号：

  ```
  netstat -tunlp|grep 端口号 
  ```

### shell

负责将用户输入的字符串转换为需要执行程序的东西叫做"shell"。即帮用户更方便使用操作系统接口的“壳”。同样的壳还有当我输入Maven+相关参数的时候是不是就会去执行相应的功能，我们驶入sql语句的时候，数据库引擎是不是也会各种调用，一样的道理

```
#!/bin/bash #告诉系统使用什么解析器
echo "Hello xiaolan !" # echo进行输出
```

- 执行方法1

```
 chmod +x ./hello.sh ./hello.sh
```

- 执行方法2

```
 /bin/sh hello.sh
```

**shell实战**

- 请将当前目录中demo.txt第二行第三列数据输出到demo2.txt中

```
 cat demo.txt|awk ’NR==2{print $3}’ >demo2.txt 
```

- 日志如下统计访问ip最多的前10个

```
awk ’{print $1}’ *.log | sort | uniq -c | sort -nr | head -n 
```

uniq - 删除排序文件中的重复行 sort对于文本进行排序 -l 按照当前环境排序. -m 合并已经排序好的文件,不排序. -n 按照字符串的数值顺序比较,暗含-b -r 颠倒比较的结果.

- 查看占用内存最大的进程的PID和VSZ

```
ps -aux|sort -k5nr|awk ’BEGIN{print ”PID VSZ”}{print ![2,(https://www.zhihu.com/equation?tex=2%2C)2,5}’|awk ’NR<3′ 
```

- 如何检查文件系统中是否存在某个文件

```
if [-f /var/log/messages]
then
echo "File exts"
fi
```

- 每个脚本开始的 #!/bin/sh 或 #!/bin/bash 表示什么意思 ?

> \#!/bin/bash 表示脚本使用 /bin/bash。对于 python 脚本，就是 #!/usr/bin/python

- &和&&区别

> “ “&” 脚本在后台运行时使用它。“&&”当前一个脚本成功完成才执行后面的命令

- 脚本文件中，如何将其重定向标准输出和标准错误流到 log.txt 文件 ?

```
./a.sh >log.txt 2>&1
```

- 如何计算本地用户的数目

```
wc -l /etc/passwd | cut -d
```

- shell中进行字符串比较和数字比较

```
[ $A == $B ] – 用于字符串比较
[ $A -eq $B ] – 用于数字比较
```

- 去掉字符串空格

> echo $string | tr -d " "

- 统计内存使用

```
#! /bin/bash
# author:xiaolan
sum=0
for mem in `ps aux |awk '{print $6}' |grep -v 'RSS' `
do
    sum=$[$sum+$mem]
done
echo "The total memory is $sum""k"
```

- 批量更改文件名

> 批量修改123目录下txt为txt.temp。将temp打包为test.tar.gz

```
#!/bin/bash
##查找txt文件
find /123 -type f -name "*.txt" > /tmp/txt.list
##批量修改文件名
for f in `cat /tmp/txt.list`
do
    mv $f $f.temp
done
##创建一个目录，为了避免目录已经存在，所以要加一个复杂的后缀名
d=`date +%y%m%d%H%M%S`
mkdir /tmp/123_$d
##把.temp文件拷贝到/tmp/123_$d
for f in `cat /tmp/txt.list`
do
    cp $f.temp /tmp/123_$d
done
##打包压缩
cd /tmp/
tar czf 123.tar.gz 123_$d/
```

### 内存泄露

* OOM内存溢出就是内存越界。内存越界有一种很常见的情况是调用栈溢出（即stackoverflow），虽然这种情况可以看成是栈内存不足的一种体现。**但内存溢出并不一定跟内存分配有什么关系，因为还有一种情况是缓冲区溢出(memory leak)。**

* 是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光。memory leak会最终会导致out of memory！

* leak原因：

  * 类管理自己的内存
  * 缓存
  * 监听器和其他回调
  * 集合类中有对对象的引用，使用完成后未清空，使得JVM不能回收；

* 解决：

  * 第一步：要验证是否存在内存泄漏

    * 1.在日志文件或者是控制台标准输出里面查找是否存在“java.lang.OutOfMemoryError”，当内存不够用的时候，JVM就会打印这个信息。

      2.把GC的日志文件上传到GCeasy，这个工具会分析出来应用是否存在内存泄漏。

      不管上面任意一个确认有问题，那么请继续看第二步。如果没有问题，那么应用可能并没有内存泄漏。

    * 第二步：打印内存映像作为基本版本

      内存映像就是Java进程使用内存的镜像，这个镜像中包含了当创建这个镜像的时候，堆里面的java对象和类的信息。当应用还处于非常健康状态的时候就应该来获取这个镜像，一般来说，应用只有在启动几分钟或者几个小时以后才会才会出现内存泄漏。因此，应用启动以后，运行10-15分钟，然后打印内存映像。
    * 第三步：打印出问题时候的内存映像

      让应用一直运行到开始出现内存问题。一旦出现内存问题，立马再次打印内存映像。那现在的问题就变成了如何才能知道应用的内存出问题了？一般来说，当内存出现问题的时候，会有下列表现：

      1.CPU利用率飙升

      2.应用无响应

      3.日志或者控制台打印java.lang.OutOfMemoryError

      因此你的应用要开启适当的监控，一旦出现上述情况立马打印内存映像。
    * 第四步：把第二步和第三步的内存映像做对比，然后分析

      把第二步打印的内存映像上传到MAT，记录下占内存最高的前5名对象，大多数时候，内存泄漏跟前5名对象有关。然后把第3步打印的内存映像上传到MAT，记录下前5名对象，如果有任何一个跟之前的不一样，那么它就是泄露的对象！现在你就知道了到底是哪些对象发生了内存泄漏！MAT可以查看到这些对象的引用，顺着这些引用链顺藤摸瓜，就能找出来到底是哪个对象哪个方法或者是哪一行代码导致的内存泄漏。

### OOM内存溢出

* 原因：
  * 有可能是内存分配确实过小，而正常业务使用了大量内存
  * 某一个对象被频繁申请，却没有释放，内存不断泄漏，导致内存耗尽
  * 某一个资源被频繁申请，系统资源耗尽，例如：不断创建线程，不断发起网络连接
* 内存中加载的数据量过于庞大，如一次从数据库中取出过多数据；
  
  * 使用的第三方软件中的bug
  
* 1、确认是不是内存本身就分配过小：

  * 方法：jmap-heap [pid]    可以查看新生代，老生代堆内存的分配大小以及使用情况，看是否本身分配过小。

* 2、找到最损耗内存的对象：

  * jmap -histo:live [pid] | more    		显示存活对象的信息，并按照所占内存大小排序

* 如果发现某类对象占用内存很大（例如几个G），很可能是类对象创建太多，且一直未释放。例如：

  - 申请完资源后，未调用close()或dispose()释放资源
  - 消费者消费速度慢（或停止消费了），而生产者不断往队列中投递任务，导致队列中任务累积过多

   方法：也可以用jmap命令dump出堆里的对象文件，使用eclipse mat分析内存对象情况

* 确认资源是否耗尽

  * 工具：查看进程创建的线程数，以及网络连接数，如果资源耗尽，也可能出现OOM。

    - pstree
    - netstat

    可以分别查看句柄详情和线程数。

    * ll /proc/${PID}/fd
    * ll /proc/${PID}/task

    句柄：

    * 0 -> 标准输入
    * 1 -> 标准输出
    * 2 -> 标准错误输出
    * 3 -> socket（容易想到是监听端口）


### CPU问题排查

* 紧急情况下尝试先重新启动服务，没问题再排查问题
* 通过**top**命令查看了当时的服务器的cpu与及内存及负载均衡的相关情况，以及找出占用cpu过高**进程的pid**
* 使用`ps -mp [pid] -o THREAD, tid, time`命令查看该进程的线程情况，发现该进程有一个线程占用率很高，找到该**线程号TID**
* 查看该线程的堆栈情况，先将**线程id转为16进制**，使用 `printf “%x\n” [tid]`命令进行转换，因为线程堆栈情况记录的是线程的16进制id：
* 然后根据线程的 ID 通过命令 `jstack pid |grep [tid] -A 30`（pid:进程id，tid:线程id），查找该线程的异常日志发现异常
* 通过**日志定位**具体问题，可能是磁盘空间问题，通过命令 `df -h`查询并且**定位该线程所在目录**
* 接下来通过命令 `du -sh *` 查看具体目录占用内存情况，发现我的项目tomcat占用了90多G，
* 最后定位问题是在tomcat中的catalina.out控制台日志输出过大，造成磁盘空间占满，然后系统无法继续写日志，从而导致刚好dubbo服务打印的日志一直处于死锁状态，该线程陷入死循环，大量消耗cpu，最终的结果是内存溢出，杀死进程
* 改bug，将控制台打印的日志给禁用掉，并限制打印日志的大小

### Git

![image-20210406151027619](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210406151027619.png)











