# 命令行环境

## 任务控制

### 结束进程

shell 会使用 UNIX 提供的信号机制执行进程间通信。当一个进程接收到信号时，它就会停止执行、处理该信号并基于信号传递的信息来改变其执行。

当我们输入 `ctrl+c` 时，shell 会发送一个 `SIGINT` 信号到进程。

### 暂停和后台执行进程

信号可以让进程做其它的事情。`SIGSTOP` 会让进程暂停。在终端键入 `Ctrl-Z` 会让 shell 发送 `SIGSTOP` 信号。

我们可以使用 `fg` 或 `bg` 命令恢复暂停的工作。分别表示在前台继续或在后台继续。`jobs` 会列出当前终端会话中尚未完成的全部任务。

命令中的后缀 `&` 可以让命令直接在后台运行，这使得我们可以直接在 shell 中继续做其它操作，不过此时它还是会使用 shell 的标准输出。

后台的进程仍然是我们终端进程的子进程，一旦我们关闭终端(发送一个信号 `SIGHUP`)， 这些后台的进程也会终止。为了防止这种情况发生，我们可以使用 `nohup` (一个用来忽略 `SIGHUP` 的封装)来运行程序。


```shell
$ sleep 1000
^Z
[1]  + 18653 suspended  sleep 1000

$ nohup sleep 2000 &
[2] 18745
appending output to nohup.out

$ jobs
[1]  + suspended  sleep 1000
[2]  - running    nohup sleep 2000

$ bg %1
[1]  - 18653 continued  sleep 1000

$ jobs
[1]  - running    sleep 1000
[2]  + running    nohup sleep 2000

$ kill -STOP %1
[1]  + 18653 suspended (signal)  sleep 1000

$ jobs
[1]  + suspended (signal)  sleep 1000
[2]  - running    nohup sleep 2000

$ kill -SIGHUP %1
[1]  + 18653 hangup     sleep 1000

$ jobs
[2]  + running    nohup sleep 2000

$ kill -SIGHUP %2

$ jobs
[2]  + running    nohup sleep 2000

$ kill %2
[2]  + 18745 terminated  nohup sleep 2000

$ jobs
```

## 终端多路复用

`tmux` TBD


## 别名

shell 的别名相当于一个长命令的缩写，shell 会自动将其替换成原本的命令。

```shell
alias alias_name="command_to_alias arg1 agr2"
```

## 配置文件

很多程序的配置都是通过纯文本格式的配置文件来完成的。

对于 bash 来说，在大多数系统下，我们可以通过编辑 `.bashrc` 或 `.bash_profile` 来进行配置。在文件中可以添加需要在启动时执行的命令，例如别名，或者环境变了。

## 远程设备

通过如下命令可以使用 `ssh` 连接到远程服务器:

```shell
ssh foo@bar.mit.edu
```

这里我们尝试以用户名 `foo` 登录服务器 `bar.mit.edu`。服务器可以通过 `URL` 也可以使用 IP 指定(foobar@192.168.1.42).

### 执行命令

`ssh foobar@serve ls` 可以直接在 foobar 中执行 ls 命令。

### ssh 密钥

- 密钥生成：使用 `ssh-keygen` 可以生成一对密钥.
