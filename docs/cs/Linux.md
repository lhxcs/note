# Linux

## 操作文件与目录

### 查看文件内容

#### cat

```shell
$ cat [OPTION] FILE # 输出 FILE 文件的所有内容
$ cat file.txt # 输出 file.txt 的全部内容
$ cat file1.txt file2.txt # 查看 file1.txt 与 file2.txt 连接后的内容
```



### 复制文件和目录

```shell
cp [OPTION] SOURCE DEST # 将 SOURCE 文件拷贝到 DEST 文件，拷贝得到的文件即为 DEST
cp [OPTION] SOURCE... DIRECTORY # 将 SOURCE 文件拷贝到 DIRECTORY 目录下，SOURCE 可以为不止一个文件
cp file1.txt file2.txt
cp file1.txt file2.txt ./file/ # 将 file1.txt、file2.txt 文件复制到同目录下的 file 目录中
cp -r dir1 ./test/ # 将 dir1 文件夹及其所有子文件复制到同目录下的 test 文件夹中
```



### 移动文件和目录

与 `cp` 的使用方式相似，效果类似于 windows 下的剪切

```shell
mv [OPTION] SOURCE DEST
mv [OPTION] SOURCE... DIRECTORY
```



### 删除文件和目录

```shell
rm [OPTION] FILE
# 如果要删除目录，需要通过 -r 选项递归删除
rm -r test/
```



### 创建目录和文件

```shell
touch FILE_NAME
mkdir DIR_NAME
```

## 进程

进程就是正在运行的程序：当我们启动一个程序时，操作系统就会从硬盘中读取程序文件，将程序内容加载入内存中，之后 CPU 就会执行这个程序。

- 查看当前运行的进程 `htop`。
- `ps` 输出进程状态，显示本终端运行的相关进程。如果需要显示所有进程，对应的命令为 `ps aux`。

### 进程标识符

进程标识符 (PID) 是进程的唯一标识。

我们平时使用操作系统的时候，可能同时会开启浏览器、聊天软件、音乐播放器、文本编辑器……前面提到它们都是进程，但是单个 CPU 核心一次只能执行一个进程。为了让这些软件看起来「同时」在执行，操作系统需要用非常快的速度将计算资源在这些进程之间切换，这也就引入了进程优先级和进程状态的概念。

- 优先级反应操作系统调度进程的手段
- htop 的显示中有两个与优先级相关的值：Priority(PRI) 和 nice (NI)。Nice 值越高表明一个进程对其它进程越友好，对应的优先级也就越低，Nice 值最高为 19，最低为 -20。通常，我们运行的程序的 nice 值为 0。