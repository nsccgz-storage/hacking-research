# Make your workflow automatic

## Why shell ?
<!-- shell vs python, pros and cons -->
The philosophy behind shell, or behind UNIX operating system is to make each program do its own part of job and then we connect this job with pipe to get our output.

Divide and conquer.
## Using shell to generate your expertiment data with bunch of arguments.

场景是这样的，现在你写好了你的创新模型的代码，比方说我写的模型叫nbKV， 你想测试你的模型的性能，首先你要确定你想测量的是什么性能指标，比方说我的nbKV 想测量的指标是write throughput, write latency, read throughput, read latency。 好， 你确定下来了你想测量的指标， 你自己用 