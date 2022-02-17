# Make your workflow automatic

## Why shell ?
<!-- shell vs python, pros and cons -->
The philosophy behind shell, or behind UNIX operating system is to make each program do its own part of job and then we connect this job with pipe to get our output.

Divide and conquer.

## 什么是shell 以及 你为什么要用shell？

打开一个命令行终端，你输入的每一行命令都只是一串字符串而已，而让这个字符串转化为系统调用exec 是由你当前使用的shell 来帮你执行的，这个就和你在终端里面输入python 然后会进入python的解释器里面一样，shell 也只是一个程序，它能做的事情就是接收你所输入的每一条指令 比如```ls``` ，然后根据你输入的这个字符串，他会去环境变量里面所有的路径下面寻找名字和你输入的字符串匹配的可执行文件，比方说我的```ls```所在的文件夹是```/bin```，这个寻找可执行文件所在的文件夹的工作也是由你的shell 帮你完成。
![image](https://user-images.githubusercontent.com/26542149/154067203-1c1f1278-3628-48de-86e5-8180c4665d2b.png)

![image](https://user-images.githubusercontent.com/26542149/154067470-fa5b28ec-09f0-424f-8904-4c16acd3f2ef.png)

目前有很多的shell 可供你使用，比方说zsh，bash， fish，作者本人用的是fish.

shell同时又是一门语言\
shell 就如同我们写的c++， java程序一样，存在变量，函数，if else while 控制语句等，所以编写shell 其实也是在写程序。

shell 的背后同时又是UNIX操作系统设计哲学的体现。


使用shell 组合各种可执行程序从而得到你想要的输出结果。\
可以理解为，每个可执行文件就是我们平常写程序中的函数，为了得到我们想要的输出结果我们就会组合这些函数同时利用if else，while 语句。 \

wordcount example

<!-- 简单举个例子，比方说我们想看一下目前挂载的设备中有哪些是挂载为dax 模式的文件系统，我们可以用如下命令\ -->
<!-- ```mount | grep dax``` -->
<!-- 在我的系统中，用mount会输出以下内容 -->
<!-- ``` -->
<!-- /dev/mapper/centos-root on / type xfs (rw,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota) -->
<!-- systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=22,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=50486) -->
<!-- fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime) -->
<!-- mqueue on /dev/mqueue type mqueue (rw,relatime) -->
<!-- debugfs on /sys/kernel/debug type debugfs (rw,relatime) -->
<!-- hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M) -->
<!-- nfsd on /proc/fs/nfsd type nfsd (rw,relatime) -->
<!-- /dev/sdc1 on /boot type xfs (rw,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota) -->
<!-- /dev/mapper/centos-home on /home type xfs (rw,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota) -->
<!-- sunrpc on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime) -->
<!-- tracefs on /sys/kernel/debug/tracing type tracefs (rw,relatime) -->
<!-- tmpfs on /run/user/1001 type tmpfs (rw,nosuid,nodev,relatime,size=19632592k,mode=700,uid=1001,gid=1001) -->
<!-- 28.10.10.6:/home/wyf/nfs on /home/wyf/nfs type nfs4 (rw,relatime,vers=4.1,rsize=1048576,wsize=1048576,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=28.10.10.3,local_lock=none,addr=28.10.10.6) -->
<!-- /dev/pmem0 on /mnt/pmem type ext4 (rw,relatime,dax=always) -->
<!-- tmpfs on /run/user/0 type tmpfs (rw,nosuid,nodev,relatime,size=19632592k,mode=700) -->
<!-- ``` -->
<!-- 上面mount 的输出是当前操作系统中所有挂载的设备的参数情况， 用其中一行为例子 -->
<!-- ``` -->
<!-- /dev/pmem0 on /mnt/pmem type ext4 (rw,relatime,dax=always) -->
<!-- ``` -->
<!-- ```/dev/pmem0 ``` 表示的是我们的pmem 的设备的路径， ```/mnt/pmem``` 表示的是```/dev/pmem0``` 挂载在```/mnt/pmem```这个文件夹下， ```type ext4``` 表示的是挂载的这块设备的文件系统时ext4 文件系统，\ -->
<!-- ```(rw,relatime,dax=always)```括号里面三项分别表示这个文件系统可读可写\ -->

<!-- 这么多行的数据让我们一行一行去看哪个设备开启了dax 模式是非常费劲的，所以我们就用上了grep, grep 的作用就是从输入的字符串中流中查找匹配我们想要的pattern 的每一行，然后再输出这些行， mount 后面跟的 ``` | ``` 表示将mount的输出pipe 给grep ，作为grep 的输入，像一个管道一样，从一个进程中流出的数据作为另一个进程的输入。\ -->
<!-- ```mount | grep dax```会输出只有和dax 匹配的行， 也就是以下输出  -->
<!-- ``` -->
<!-- [root@cn3 ~]# mount | grep dax -->
<!-- /dev/pmem0 on /mnt/pmem type ext4 (rw,relatime,dax=always) -->
<!-- ``` -->

<!-- ##  shell 和我们写的C++程序有什么不同之处？ -->
<!-- 1. 数据类型 -->
<!-- 2. 空格很严格，不该有空格的地方不能有，该有的必须有 -->

<!-- 变量的类型相对来说更少了，shell 里面只存在两种数据类型，string 和number， 没有 -->

## Using shell to generate your experiment data with bunch of arguments.

场景是这样的，现在你有一个可执行文件叫db_bench，这个db_bench能够做的事情是测量db的各种性能指标，比方说， write throughput:MB/s, write latency:micros/op, read throughput:MB/s, read latency:micros/op。 
调用db_bench 的命令如下
```bash
./db_bench --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=/mnt/pmem
```

执行完这条命后会得到如下输出，
```
LevelDB:    version 1.23
Date:       Wed Feb 16 20:23:57 2022
CPU:        40 * Intel(R) Xeon(R) Gold 6230N CPU @ 2.30GHz
CPUCache:   28160 KB
Keys:       16 bytes each
Values:     1024 bytes each (512 bytes after compression)
Entries:    1000000
RawSize:    991.8 MB (estimated)
FileSize:   503.5 MB (estimated)
WARNING: Optimization is disabled: benchmarks unnecessarily slow
WARNING: Assertions are enabled; benchmarks unnecessarily slow
WARNING: Snappy compression is not enabled
------------------------------------------------
fillrandom   :      38.263 micros/op;   25.9 MB/s
readseq      :       1.436 micros/op;  690.9 MB/s
readrandom   :       9.164 micros/op; (631820 of 1000000 found)
```
<!-- ![image](https://user-images.githubusercontent.com/26542149/154267492-7278b9f5-b0b2-4e8f-acca-2ef371681617.png) -->

解释一下上面命令的各个参数的意思，```--benchmarks=fillrandom,readseq,readrandom```表示我们希望测量随机写，顺序读，随机读的写入延迟和吞吐， ```--value_size=1024```表示写入的一个kv pair的value 的size 是1024 个字节， ```--db=/mnt/nvme_ssd```表示数据库的文件都会写入```/mnt/nvme_ssd```这个目录下

现在我们有ssd, hdd, pmem三种存储介质，我们想在这三种存储介质上面都跑一下上面的命令。\
你会怎么做? 在命令行里面依次输入三条命令 ？ 
```bash

$ ./db_bencch --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=/mnt/nvme_ssd
$ ./db_bencch --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=/mnt/hdd
$ ./db_bencch --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=/mnt/pmem

```
看起来还可行，只需要在每次命令结束之后，再输入下一条命令就可以了，在一条命令执行期间，你可以做一些别的事情，时不时回来看一下当前的命令有没有结束。

但是我们可以让shell 自动的帮我们跑上面三个命令。
在你的终端里面输入以下命令就可以做到让shell 自动的帮你跑三条命令。
```bash
$ devices=("/mnt/pmem" "/mnt/nvme_ssd" "/mnt/hdd")
$ for device in ${devices[@]}; do 
> bench_cmd="./db_bencch --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=$device"
> echo $bench_cmd
> eval $bench_cmd
> done
```
解释一下这两条命令。
```
$ devices=("/mnt/pmem" "/mnt/nvme_ssd" "/mnt/hdd")
```
这条命令就是相当于声明了一个数组，里面的每个字符串就是我们不同设备的路径。每个字符串之间必须用空格分开，不然的话，shell 会把这三个字符串当成一个字符串。\
另一个需要特别注意的就是等号两边不能有空格，如果你写成空格的话，shell 会有如下输出
```
bash: syntax error near unexpected token `('
```
因为加了空格会让shell 解释成```devices``` 是一个命令，而不是一个变量。 所以在进行变量赋值的时候一定不能加空格。

```bash
$ for device in ${devices[@]}; do 
> bench_cmd="./db_bencch --benchmarks=fillrandom,readseq,readrandom --value_size=1024 --db=$device"
> echo $bench_cmd
> eval $bench_cmd
> done
```
解释一下上面的for loop， 这个和python里面的for loop差不多，需要注意的地方有，访问数组必须是```${devices[@]}```，用```$```
表示我们想访问这个变量，就和c++ 里面指针的解引用要在变量前面加一个```*```一样，```$```后面必须用```{}```将你的变量括起来，如果不用```{}```的话
shell会将```$devices```解释成```devices```数组的第一个元素，同时要在变量的后面加上```[@]```表示我们想访问这个数组的所有元素
如果你只想访问数组的某一个元素，你可以将```@```替换成对应元素的下标就可以了，比方说```${devices[2]}```就是访问数组的第三个元素。

for loop第二个需要注意的点是```for device in ${devices[@]}```后面必须跟一个```;``` 或者跟一个换行符也就是敲击一下回车键才可以在for 语句后面跟上do 开始我们的
循环逻辑，结束for loop 要加一个```done```

你也可以用数组下标的方式来访问数组元素
```bash
for((i=0;  i < ${#devices[@]}; i++)); do
> echo ${devices[i]}
> done
```
```${#devices[@]}```表示数组的元素的个数，同时另一个需要注意的地方是```for``` 后面跟```(( ))```可以执行算数运算。

现在请你在你自己的终端里面输入上面的命令试一下。


学会了变量申明和for loop之后，我们就可以进行for loop 的嵌套来测试不同的不同的设备，不同的value_size，不同的写入方式的性能数据了



<!-- 现在我们想测试一下db 在不同存储介质下的表现情况，以及不同的value_size 下的各种性能情况，以及单线程和多线程的性能情况。 -->



<!-- 为了得到这么多种的测试组合的结果，你可以选择在terminal 一条一条输入命令，但是你不想这么做，这种重复的工作计算机最适合做，所以接下来的内容就是有关怎么样让计算机来做这些重复的步骤。 -->

<!-- 在写shell 脚本之前和写任何的代码和项目一样，我们要做的第一件事情是，明确我们的输入和输出， -->
<!-- 我们的输入是log path， 对于shell 脚本来说， 最终我们想要得到的是带有db\_bench输出的所有字符，然后 -->
<!-- 我们想从这些字符中提取我们想要的性能数据，提取数据这一部分放在下一节来讲。  -->

<!-- a db_bench result scrrenshot, imply that we don't need to input the data to the excel or something to extrac the data out, we could do it by using grep or awk or sed, put this screenshot into data_extract.md  -->

代码如下

下面代码是保存在文件```bench.sh```里面.
<!-- default value ${1:-"/home/nbkv/log"} -->
```bash 
#!/bin/bash

# accept log directory from user input  
LOG_DIR=${1:-"/home/kv/log"}

# check existence of directory, if not exist,  we will create it.
if [ ! -d ${LOG_DIR}]; then
  mkdir -p ${LOG_DIR}
fi



WRITE_BENCHS=("fillrandom" "fillseq")
DEVICES=(
"pmem /mnt/pmem"
"ssd /mnt/nvme_ssd"
"hdd" "mnt/hdd"
)
VALUE_SIZES=(64  256 1024 2048)

for value_size in ${VALUE_SIZES[@]}; do
  for write_bench in ${WRITE_BENCHS[@]}; do
    for device in "${DEVICES[@]}"; do
      #create array from string  
      device_pair=(${device})

      # log file path 
      log_path="${LOG_DIR}/${device_pair[0]}.${bench_name}.${value_size}.log"
      bench_cmd="db_bench  --benchmarks=${bench_name} --value_size=${value_size} --db=${device_pair[1]} \
      2>&1 | tee ${log_path}"

      echo ${bench_cmd}
      eval ${bench_cmd}
    done

  done
done

echo "all done"

```
解释一下上面代码新出现的东西

第一个
```
# accept log directory from user input  
LOG_DIR=${1:-"/home/kv/log"}
```
当我们在执行我们的脚本文件的时候，我们可能会想要指定我们的日志存放的目录，这个时候我们的在终端的调用命令可能是\
```./bench.sh /tmp/log``` 脚本获得用户输入的参数方式就是```$1``` 来获得第一个参数，也就是```/tmp/log```, 同理，我们想获得
第二个参数的方式就是```$2```，以此类推\
如果用户没有给定输入路径的话，那么我们就会用一个默认的路径也就是```/home/kv/log```，申明方式是```:-```后面跟你的默认路径。  

第二个
```
# check existence of directory, if not exist,  we will create it.
if [ ! -d ${LOG_DIR} ]; then
  mkdir -p ${LOG_DIR}
fi
```
在我们想把我们的log 文件写入到某个目录下时，我们要先确保这个目录是存在的，这个时候我们就可以用```[```
来检查给定的目录路径是否存在了.\
```[``` 实际上是```test``` 的别名， 所以```[``` 也是一个可执行程序，你可以在终端里面输入```which [```来看看```[```存在哪个
目录下\
需要注意的有两点，第一， 你必须在每个字符串之间加上空格，我加了SPACE特别标注了空格```if SPACE [ SPACE ! SPACE -d SPACE ${LOG_DIR} SPACE];```
如果你写成```[!```，那么shell 会将```[!```看成一个命令，而这个命令是不存在。\
第二，```[``` 结尾必须跟```]```


第三个，
```
bench_cmd="db_bench  --benchmarks=${bench_name} --value_size=${value_size} --db=${device_pair[1]} \
      2>&1 | tee ${log_path}"
```
我们先将我们要执行的命令字符串存在变量bench_cmd里面，这么做的好处是，我们可以先echo 一下，看看命令的变量替换是否正确，然后
我们再执行命令。

然后是``` | tee ${log_path}```，这段代码的意思是，接受前面命令的输出作为```tee```的输入，然后将输入内容写入到```${log_path}```
文件中，同时还会被输出到stdout 中，也就是我们的终端里面，也就是输出了两个目的地，这么做的好处是我们既可以看到程序在正常的执
行，又可以将原有的输入写入到文件中，方便我们后续进行数据处理。

额外说一句，使用```tee```还有另外一个好处，就是当我们想要将一个命令的输入输出到一个需要root 权限才能访问的目录下的文件夹时
，```tee``` 可以帮我们做到这一点，比方说```echo 3 | sudo tee /root/test``` ， 而```sudo echo 3 >> /root/test ```则会报错说
你没有root权限，原因是```>>``` 输出重定向是由操作系统帮我们完成的，```echo``` 不负责输出重定向的部分，即使加```sudo ``` 也没用。


c++, python or shell?
what shell really does is to connect all commands, not to implement your business logic

some tutorial link








## 想稍微系统性的学习一下shell ？
看看这个链接, 这个教程只有十几篇，每篇都很简短，足以应付日常编写shell 的需求。
[shell script tutorial](https://www.shellscript.sh/)

## References
