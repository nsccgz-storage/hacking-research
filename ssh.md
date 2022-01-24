

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
2.

# reference
[ssh tutorial 中文](https://wangdoc.com/ssh/port-forwarding.html)

[All illustrated guide to ssh tunnels](https://solitum.net/posts/an-illustrated-guide-to-ssh-tunnels/)

[frp reverse proxy tool](https://github.com/fatedier/frp#example-usage)
