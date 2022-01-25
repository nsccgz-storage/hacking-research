# Make your workflow automatic

## Why shell ?
<!-- shell vs python, pros and cons -->
The philosophy behind shell, or behind UNIX operating system is to make each program do its own part of job and then we connect this job with pipe to get our output.

Divide and conquer.
## Using shell to generate your expertiment data with bunch of arguments.

场景是这样的，现在你写好了你的创新模型的代码，比方说我写的模型叫nbKV， 你想测试你的模型的性能，首先你要确定你想测量的是什么性能指标，比方说我的nbKV 想测量的指标是write throughput, write latency, read throughput, read latency。 好， 你确定下来了你想测量的指标， 你自己用 

为了得到这么多种的测试组合的结果，你可以选择在terminal emulator一条一条输入命令，但是你不想这么做，这种重复的工作计算机最适合做，所以接下来的内容就是有关怎么样让计算机来做这些重复的步骤。

在写shell 脚本之前和写任何的代码和项目一样，我们要做的第一件事情是，明确我们的输入和输出，
我们的输入是log path， 对于shell 脚本来说， 最终我们想要得到的是带有db\_bench输出的所有字符，然后
我们想从这些字符中提取我们想要的性能数据，提取数据这一部分放在下一节来讲。 

```
log path

default value ${1:-"/home/nbkv/log"}


for
for

    db_bench --thread=${read_num} --bench=${bench_name} 2>&1 | tee ${log_path}
done
done

echo
tee

```


c++, python or shell?
what shell really does is to connect all commands, not to implement your business logic

some tutorial link







