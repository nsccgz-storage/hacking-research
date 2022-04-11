# ssh 端口转发以及直连跳板机背后的服务器

本教程假设你对ssh config 文件配置有基本认识， 知道怎么利用ssh config 文件 连接远程节点。
## ssh 直连服务器

现在遇到的问题是这样的， 就是我们想登陆到我们实验用的服务器时需要先登陆到一台跳板机上，然后再
从跳板机上登陆到服务器。
```
# 
$ ssh root@jumphost 

# 在跳板机上执行以下命令
ssh root@server

```

我们可以在自己的电脑上做一下简单配置，实现一条命令直接连接我们的跳板机
```

ssh root@server -J jumphost

```
上面的命令```-J jumphost```主要做的事情就是告诉ssh 命令它想连接server 服务器先要经过中间的跳板机， 这样就不用每次
都输入两次命令才能连接跳板机了。

当然，如果你和目标服务器之间隔了很多个跳板机，我们也可以做到只要一条命令就直连跳板机的，
栗子如下，
打开你的```~/.ssh/config```文件
在里面输入以下内容，
```
# Host 表示我们给我们想连接的服务器取的名字
Host jumphost1 
  # HostName 表示服务器的ip地址
  HostName  172.38.58.12  
  # 登陆jumphost1 用到我们的密钥，这样不用每次登陆都输入密码。
  IdentityFile ~/.ssh/id_rsa

Host jumphost2
  HostName 28.10.12.13
  # ProxyJump jumphost1 表示我们想登陆jumphost2 必须要先通过jumphost1 代理
  ProxyJump jumphost1

Host server
  HostName 192.16.38.20
  # 我们想登陆 server 服务器必须要先经过jumphost2， jumphost2 又会先经过jumphost1 跳板机代理
  ProxyJump jumphost2
```

保存上面文件之后， 我们就可以实现在终端输入下面一行命令直接登陆我们的服务器了。
```$ ssh server```

还有一行配置```   IdentityFile ~/.ssh/id_rsa ```, 要让
这行配置生效我们得先将我们的公钥 拷贝到服务器上，
 
 执行这行命令``` $ ssh-copy-id -i ~/.ssh/id_rsa.pub user@hostname```
将我们的公钥拷贝到服务器上，
如果你目前的电脑上没有公钥， 你还需要先执行下面的命令
``` $ ssh-keygen  ```

## ssh 端口转发

前面直连服务器又一个前提是相邻两个服务器之间的连通方向是一样的， 也就是下面这幅图
![image](https://user-images.githubusercontent.com/26542149/162617599-75019d7c-d4f9-49c9-8b8e-2ac3d4b255ea.png)
可以看到， 这种连通关系下nodeA
可以让我利用上面的ssh 配置文件直接连接nodeC，

那如果是下面这样的连通情况呢？nodeA 可以直接连接nodeB
， nodeC也可以直接连接nodeB， nodeA 不能直接连接nodeC，
上述的问题举个栗子就是，nodeB 是一台具有公网ip 的服务器，而nodeA 和nodeC 都是在局域网中，相互无法直接连接对方。

![image](https://user-images.githubusercontent.com/26542149/162619574-57c8c173-19b1-4576-887e-a1b4b65742f5.png)

那我们怎么让nodeA 可以直接ssh 登陆nodeC呢？ 这就要
利用到我们的ssh端口转发功能。

实际上上面的问题可以简化为让nodeB 可以直接连接nodeC， 这样nodeA 就有一条完整的通路可以直接连接nodeC了。


在nodeC 上执行一下命令
```
# enter following command at nodeC
$ ssh -R 4444:localhost:22 nodeB

or 
$ ssh -R 4444:localhost:22  root@28.10.10.45

```
上面的命令做的事情就是将nodeB 上4444 端口的所有
请求全部转发的nodeC 上的22 端口，也就是说
我现在在nodeB上执行以下命令就可以ssh 登陆到nodeC上
```
# enter the following command at nodeB
$ ssh root@localhost -p 4444 
```
，这样我们就有了一条nodeB 到nodeC 的连接通路了， which means， 我们可以让nodeA 直连nodeC 了，现在node 连接
情况图片是这样的。
![image](https://user-images.githubusercontent.com/26542149/162620569-a18f0ab6-0ab6-4c11-b28e-cca9599a8caa.png)


我稍稍解释一下上面命令每个参数的意思， ```-R``` 示我们将要执行remote forward 远程端口转发
，就是将远端的端口转发到本地节点所知的某个节点的端口。

然后是```4444:localhost:22```， ```4444```表示远程节点nodeB上的中间代理端口，```locahost```
表示将4444 端口转发到的节点的ip 地址是本地节点， 也就是nodeC， 你也可以将```localhost``` 替换
成nodeC 所知的其他节点的ip地址， 然后是```22```表示 nodeC 上的22 端口。
最后的```nodeB``` 或者```root@28.10.10.45``` 表示 远程节点nodeB 的ssh 配置信息，包括ip地址和用户名，表示远程```4444```端口所属的节点的ip地址是哪个。


<!-- 现在遇到的问题是这样的， -->

现在你就可以在nodeA 的终端里面执行以下命令直接连接nodeC 了
```
# enter the following command at nodeA  
$  ssh root@28.10.10.45 -p 4444
```
上面的命令意思就是说现在我们只要ssh 连接nodeB 上的4444 端口就等于ssh 连接了nodeC。


ssh 远程端口转发的另一个实用场景是流量代理， 就是做到和vpn 一样的功能。
场景如下，现在我们在内网的服务器nodeC上不能访问baidu google 等网站，登陆服务器也只能通过跳板机登陆，
所以我们的网络连通图是这样的，


现在nodeA上，也就是我们自己的电脑是可以访问google baidu 的，我们自己电脑上的代理端口是1087， 也就是
```
http_proxy=127.0.0.1:1087 curl google.com
```
这样是可以正常返回的， 你可以在自己的电脑上试一下 上面的命令。

然后，我们现在想让位于内网中的服务器nodeC 访问google，或者可以执行```git clone ``` 命令，那么我们要做的
事情就是想办法将nodeC 上的某个端口远程转发到我们自己的电脑nodeA 上的1087 端口。

我们要执行的命令就是下面这一条，
```
# enter the following command at nodeA
$ ssh -Nf -R 5555:localhost:1087 nodeC 

or 
$ ssh -Nf -R 5555:localhost:1087 root@28.10.10.35 -J nodeB

```
上面的命令就是执行ssh 远程端口转发，将nodeC 上的发送给5555端口的网络包全部转发到本地电脑nodeA 上的1087
端口， 这样就实现了位于内网的服务器可以访问google 或者执行```git clone ``` 命令了。

第一条命令执行成功的前提是，你在```~/.ssh/config``` 文件里面配置好了nodeC 和nodeB 相关的ip 信息和连接信息。
比如是这样
```
# file path is ~/.ssh/config

Host nodeB
  HostName 172.38.49.50
  User root
  IdentityFile ~/.ssh/id_rsa


Host nodeC 
  HostName 28.35.10.10
  User root
  IdentityFile ~/.ssh/id_rsa
  ProxyJump nodeB

```
在```~/.ssh/config``` 文件里面添加好上面两条配置信息，
然后要让你可以利用IdentityFile 免密执行ssh 命令，
你还需要执行之前说的
```ssh-copy-id nodeB```
```ssh-copy-id  nodeC```
这两条命令就可以了。

现在，你自己来试一下上面学到的命令吧。


## potential problems 
1. ssh login still require password after ssh-copy-id
    linux file/directory permission for owner/group/other is not the same for your
    $HOME directory and $HOME/.ssh directory.
    Try commands below
    ```
    chmod 700 ~/
    chmod 700 ~/.ssh
    ```
    上面这两条命令做的事情就是只让home 目录的owner 具有读写权限，而group，other 用户没有读写权限，并且让他们的读写权限都是一样的，不会出现$home 目录出现了group 用户具有读写权限， 而$home/.ssh 目录对group 用户却没有读写权限这种矛盾的情况。 
    
    The reason behind command above is as follow, and I quote
    > The logic to the above (which helps explain WHY you do it, which then helps me remember to do it) is this: the g+w permission on a directory lets you create, move, or rename its contents. Thus if your .ssh dir is set to g-w, but $HOME is set to g+w... then someone in that group could rename your .ssh dir to .junk (because g+w on $HOME allows that), and create a new (and "fraudulent") .ssh directory. – Dan H
    
    就是说home 目录的读写权限对于owner/group/other user 和$home/.ssh 目录的读写权限对于owner/group/other user 不一样，导致$home/.ssh 存在被group user 恶意篡改的风险 

    [ssh prompts for password despite ssh-copy-id](https://unix.stackexchange.com/questions/4484/ssh-prompts-for-password-despite-ssh-copy-id)
    
2. ip address of Host name in config file is not static 
    在使用wsl 的时候遇到这样一个问题， 就是每次登录wsl 的时候wsl 的ip地址和host windows 的ip地址都会发生变化， 这个时候你在ssh config 里面的ip address 就要想办法动态的改变，或者我们不使用ssh config 文件里面的信息，改为直接用shell 命令，这样也可以做到动态的替换ip address
    两种思路解决wsl host ip address 动态变化的问题：
    
    1. 在启动shell 的时候读取host ip address， 然后用sed 命令替换掉ssh config 文件host ip address
    2. 不使用ssh config 文件，改为直接在shell 中使用ssh 命令， 将host ip address 存在环境变量中。
    3. 不使用ip地址，改为使用域名，由dns 服务器返回对应的ip地址

    [use bash variable in ssh config](https://stackoverflow.com/questions/32528735/using-bash-variable-in-ssh-config)

# reference
[ssh tutorial 中文](https://wangdoc.com/ssh/port-forwarding.html)

[All illustrated guide to ssh tunnels](https://solitum.net/posts/an-illustrated-guide-to-ssh-tunnels/)

[frp reverse proxy tool](https://github.com/fatedier/frp#example-usage)
