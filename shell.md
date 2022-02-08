# Make your workflow automatic

## Why shell ?
<!-- shell vs python, pros and cons -->
The philosophy behind shell, or behind UNIX operating system is to make each program do its own part of job and then we connect this job with pipe to get our output.

Divide and conquer.

## 什么是shell 以及 你为什么要用shell？
打开一个命令行终端，你输入的每一行命令都只是一串字符串而已，而让这个字符串转化为系统调用exec 是由你当前使用的shell 来帮你执行的，这个就和你在终端里面输入python 然后会进入python的解释器里面一样，shell 也只是一个程序，它能做的事情就是接收你所输入的每一条指令 比如```ls``` ，然后根据你输入的这个字符串，他会去环境变量里面所有的路径下面寻找名字和你输入的字符串匹配的可执行文件，比方说我的```ls```所在的文件夹是```/bin```，这个寻找可执行文件所在的文件夹的工作也是由你的shell 帮你完成。
目前有很多的shell 可供你使用，比方说zsh，bash， fish，作者本人用的是fish，它具有输入命令自动补全功能。

shell同时又是一门语言

shell 的背后同时又是UNIX操作系统设计哲学的体现。

使用shell 组合各种程序从而得到你想要的输出结果。

## Using shell to generate your expertiment data with bunch of arguments.

场景是这样的，现在你写好了你的创新模型的代码，比方说我写的模型叫nbKV， 你想测试你的模型的性能，首先你要确定你想测量的是什么性能指标，比方说我的nbKV 想测量的指标是write throughput, write latency, read throughput, read latency。 好， 你确定下来了你想测量的指标， 你自己用 