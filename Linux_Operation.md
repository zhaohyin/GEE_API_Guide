# Linux Command
<!-- TOC -->

- [Linux Command](#linux-command)
    - [Server Operation](#server-operation)
        - [连接远程服务器时常使用的命令,一般情况下就是使用最为基础的文档。](#%E8%BF%9E%E6%8E%A5%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%97%B6%E5%B8%B8%E4%BD%BF%E7%94%A8%E7%9A%84%E5%91%BD%E4%BB%A4%E4%B8%80%E8%88%AC%E6%83%85%E5%86%B5%E4%B8%8B%E5%B0%B1%E6%98%AF%E4%BD%BF%E7%94%A8%E6%9C%80%E4%B8%BA%E5%9F%BA%E7%A1%80%E7%9A%84%E6%96%87%E6%A1%A3)
        - [从本地上传文件到服务器 [一般常用]](#%E4%BB%8E%E6%9C%AC%E5%9C%B0%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6%E5%88%B0%E6%9C%8D%E5%8A%A1%E5%99%A8-%E4%B8%80%E8%88%AC%E5%B8%B8%E7%94%A8)
        - [从远程复制到本地](#%E4%BB%8E%E8%BF%9C%E7%A8%8B%E5%A4%8D%E5%88%B6%E5%88%B0%E6%9C%AC%E5%9C%B0)
    - [Common Linux Command](#common-linux-command)
        - [Linux服务器里常用的命令](#linux%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%87%8C%E5%B8%B8%E7%94%A8%E7%9A%84%E5%91%BD%E4%BB%A4)
        - [环境配置时高效命令](#%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E6%97%B6%E9%AB%98%E6%95%88%E5%91%BD%E4%BB%A4)
        - [实战时发现的新命令](#%E5%AE%9E%E6%88%98%E6%97%B6%E5%8F%91%E7%8E%B0%E7%9A%84%E6%96%B0%E5%91%BD%E4%BB%A4)

<!-- /TOC -->

## 1.Server Operation


### 1.1  连接远程服务器时常使用的命令,一般情况下就是使用最为基础的文档。

    > ssh[-p port] user@remote 

    > eg. ssh yinzhaohui@10.0.68.110 

    > 接下来就是输入服务器的账号密码即可


<p align=right><button>Wed Jan 6</button> </p>

---
### 1.2 从本地上传文件到服务器 [一般常用]

    > scp [可选参数] file_source file_target \

    > scp 11.txt yinzhaohui@10.0.68.110:Desktop/11.txt


```zsh
scp local_file remote_username@remote_ip:remote_folder [不指定文件名]

scp local_file remote_username@remote_ip:remote_file [指定文件名]

scp local_file remote_ip:remote_folder [不指定文件名]

scp local_file remote_ip:remote_file [指定文件名]
```
    上传文件到远端
    > scp /home/space/music/1.mp3 www.runoob.com:/home/root/others/music/001.mp3 

    上传文件夹到远端
    > scp -r /home/space/music/ root@www.runoob.com:/home/root/others/ 


<p align=right><button>Wed Jan 6</button> </p>

---

### 1.3 从远程复制到本地

```zsh
# 从远程复制到本地，只要将从本地复制到远程的命令的后2个参数调换顺序即可
scp root@www.runoob.com:/home/root/others/music /home/space/music/1.mp3 

# 如果远程服务器防火墙有为scp命令设置了指定的端口，我们需要使用 -P 参数来设置命令的端口号，命令格式如下：
# scp 命令使用端口号 4588
scp -P 4588 remote@www.runoob.com:/usr/local/sin.sh /home/administrator
```

<p align=right><button>Wed Jan 6</button> </p>
&nbsp;

## 2 Common Linux Command 
### 2.1 Linux服务器里常用的命令
太过简单，再次不做赘述
   > cd \
   > mkdir \
   > rm \
   > mv \
   > cp \
   > ls [-a] [-h] [-l] \


<p align=right><button>Wed Jan 6</button> </p>


### 2.2 环境配置时高效命令
```zsh
# 查看模块所在位置
which python
# 查看模块的版本号
python --version
# 帮助
python --help
```

<p align=right><button>Wed Jan 6</button> </p>


### 2.3 实战时发现的新命令
```zsh
# 服务器设置权限
# r(4)/w(2)/x(1)分别代表读(Red)、写(Write)、执行(Excute)
chmod [可选项] <mode> file

# u,g,o分别代表用户(user)、群组(Group)、其他用户（Other）
# 栗子1:设置用户对该文件夹拥有读写和执行权限
chmod u+rwx ~/Desktop/LibriMix 

# 栗子2:设置群组对该文件夹拥有读取和执行权限
chmod g+rx ~/Desktop/LibriMix

# 栗子3：同时设置则需要利用二进制
# -rw------- (600)    只有拥有者有读写权限。
# -rw-r--r-- (644)    只有拥有者有读写权限；而属组用户和其他用户只有读权限。
# -rwx------ (700)    只有拥有者有读、写、执行权限。
# -rwxr-xr-x (755)    拥有者有读、写、执行权限；而属组用户和其他用户只有读、执行权限。
# -rwx--x--x (711)    拥有者有读、写、执行权限；而属组用户和其他用户只有执行权限。
# -rw-rw-rw- (666)    所有用户都有文件读、写权限。
# -rwxrwxrwx (777)    所有用户都有读、写、执行权限。

chmod 700 ~/Desktop/LibriMix
```

<p align=right><button>Wed Jan 6</button> </p>

