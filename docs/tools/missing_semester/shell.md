# Shell

## 使用 Shell

```bash
missing: ~$
```

这是 shell 最主要的文本接口。
- 它告诉我们主机名是 `missing` 并且当前的工作目录是 `~`( 表示 home )。
- `$` 表示我们现在的身份不是 root 用户。

**shell 如何知道去哪里寻找 `data` 或 `echo` ?**

- shell 是一个编程环境，它具备变量、条件、循环和函数。
- 在 shell 执行命令时，实际上是在执行一段 shell 可以解释执行的简短代码。
- 如果 shell 执行的指令不是 shell 所了解的编程关键字，那么它会去咨询环境变量 `$PATH`，它会列出当 shell 接到某条指令时，进行程序搜索的路径

```bash
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ which echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

当我们执行 echo 命令时，shell 了解到需要执行 echo 这个程序，随后它便会在 $PATH 中搜索由 : 所分割的一系列目录，基于名字搜索该程序。当找到该程序时便执行（假定该文件是可执行程序）。确定某个程序名代表的是哪个具体的程序，可以使用 which 程序。我们也可以绕过 $PATH，通过直接指定需要执行的程序的路径来执行该程序。(Not fully understand)

### 在 shell 中导航

- shell 中的路径是一组被分割的目录，在 Linux 和 macOS 上使用 `/` 分割。
- 路径 `/` 代表的是系统的根目录，所有的文件夹都包括在这个路径之下。
- 当前工作目录可以使用 `pwd` 来获取。
- `.` 表示当前目录，`..` 表示上级目录。

```bash
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

`ls -l` 可以更加详细列出目录下文件或文件夹的信息。

- 第一个字符 d 表示 `missing` 是个目录
- 接下来的九个字符中，三个字符构成一组
    - 分别代表了文件所有者(missing), 用户组(users) 以及其他人所具有的权限。
    - `-` 代表该用户不具备相应的权限。
    - 只有文件所有者可以修改 `w`
    - 为了进入某个文件夹，用户需要具备该文件夹以及其父文件夹的搜索权限(以可执行 `x` 权限表示)。
    - 为了列出它包含的内容，用户必须对该文件具备读权限 `r`。 

### 在程序间创建连接

- 在 shell 中程序主要有两个流：输入流和输出流。
- 从输入流中读取信息，将信息输出到输出流。
- 通常，程序的输入输出流都是终端(键盘输入，显示器输出)。
- 但是我们可以重定向！

```bash
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello
missing:~$ cat < hello.txt
hello
missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

`|` 可以将一个程序的输出和另一个程序的输入连接起来。

```bash
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2
219
```

### 根用户

只有根用户才能向 `sysfs` 文件写入内容。系统被挂载在 `/sys` 下， `sysfs` 文件则暴露了一些内核参数。

TBD

!!! note "Homework1"

    1. 用 `touch` 在 `missing` 文件夹中新建一个叫 `semester` 的文件
    2. 将以下内容一行一行写入 `semester` 文件

    ```shell
    #!/bin/sh
    curl --head --silent https://missing.csail.mit.edu
    ```

    3. 尝试执行这个文件。例如，将该脚本的路径（./semester）输入到您的 shell 中并回车。如果程序无法执行，请使用 ls 命令来获取信息并理解其不能执行的原因。
    4. 使用 chmod 命令改变权限，使 ./semester 能够成功执行，不要使用 sh semester 来执行该程序。您的 shell 是如何知晓这个文件需要使用 sh 来解析呢？
    5. 使用 | 和 > ，将 semester 文件输出的最后更改日期信息，写入主目录下的 last-modified.txt 的文件中

## Shell 脚本

- bash 中为变量赋值的语法是 `foo=bar` （不能有空格，有空格的话解释器会调用程序 foo 并将 = 和 bar 作为参数。）
- 访问变量中存储的数值语法为 `$foo`。
- bash 中的字符串
    - `'` 为原义字符串，其中的变量不会被转义
    - `''` 定义的字符串会将变量值进行替换

```bash
foo=bar
echo "$foo"
# 打印 bar
echo '$foo'
# 打印 $foo
```

- bash 中的函数，下面的函数创建一个文件夹并进入该文件夹

```bash
mcd() {
    mkdir -p "$1"
    cd "$1"
}
```

- 这里 `$1` 是脚本的第一个参数。bash 使用很多特殊的变量来表示参数等：
    - `$0` 脚本名
    - `$1` 到 `$9` 脚本的参数
    - `$@` 所有参数
    - `$#` 参数个数
    - `$?` 前一个命令的返回值
    - `$$` 当前脚本的进程识别码
    - `!!` 完整的上一条命令。(当权限不足执行命令失败时，可以 `sudo !!` 再试一次)
    - `$_` 上一条命令的最后一个参数
- 命令通常使用 `STDOUT` 来返回输出值，用 `STDERR` 返回错误及错误码。返回值为 0 表示正常执行，其它所有非 0 返回值都表示有错误发生。
- 退出码可以搭配 `&&` 和 `||` 来使用进行条件判断。`true` 的返回码永远是 0， `false` 的返回码永远是 1.

```bash
false || echo "Oops, fail"
# Oops, fail (如果前一个命令失败，就执行后一个命令)

true || echo "Will not be printed"
#

true && echo "Things went well"
# Things went well (如果前一个命令成功，就执行后面的命令)

false && echo "Will not be printed"
#

false ; echo "This will always run"
# This will always run
```

- 我们还可以以变量的形式获取一个命令的输出，这可以通过命令替换实现
    - `$(CMD)` 来执行 CMD 命令时，输出结果会替换掉 `$(CMD)`
    - `for file in $(ls)`: shell 首先将调用 ls ，然后遍历得到的这些返回值。

```bash
#!/bin/bash

echo "Starting program at $(date)" # date会被替换成日期和时间

echo "Running program $0 with $# arguments with pid $$"

for file in "$@"; do
    grep foobar "$file" > /dev/null 2> /dev/null
    # 如果模式没有找到，则grep退出状态为 1，如果找到,grep退出状态为0(代表成功)
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息
    #2> 代表重定向标准错误输出
    if [[ $? -ne 0 ]]; then
        echo "File $file does not have any foobar, adding one"
        echo "# foobar" >> "$file"
    fi
done
```

这段脚本通过遍历输入的文件列表，检查每个文件是否包含 `foobar` 字符串。如果某个文件没有找到，它会在文件末尾添加 `# foobar`。

### shell 的通配(globbing)

当执行脚本时，我们经常需要提供形式类似的参数。bash 使我们可以轻松实现这一操作，它可以基于文件扩展名展开表达式。

- 通配符：使用 `?` 和 `*` 来匹配一个或任意个字符。例如，对于文件 foo, foo1, foo2, foo10 和 bar, rm foo? 这条命令会删除 foo1 和 foo2 ，而 rm foo* 则会删除除了 bar 之外的所有文件。
- 花括号`{}`： 当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。这在批量移动或转换文件时非常方便。

```bash
convert image.{png,jpg}
# 会展开为
convert image.png image.jpg

cp /path/to/project/{foo,bar,baz}.sh /newpath
# 会展开为
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath

# 也可以结合通配使用
mv *{.py,.sh} folder
# 会移动所有 *.py 和 *.sh 文件

mkdir foo bar

# 下面命令会创建 foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h 这些文件
touch {foo,bar}/{a..h}
touch foo/x bar/y
# 比较文件夹 foo 和 bar 中包含文件的不同
diff <(ls foo) <(ls bar)
# 输出
# < x
# ---
# > y

```

## Shell 工具

### 查找文件

- `find` 命令递归搜索符合条件的文件

```bash
# 查找所有名称为src的文件夹
find . -name src -type d
# 查找所有文件夹路径中包含test的python文件
find . -path '*/test/*.py' -type f
# 查找前一天修改的所有文件
find . -mtime -1
# 查找所有大小在500k至10M的tar.gz文件
find . -size +500k -size -10M -name '*.tar.gz'
```

- 除此之外，find 还能对所有查到的文件进行操作。

```bash
# 删除全部扩展名为.tmp 的文件
find . -name '*.tmp' -exec rm {} \;
# 查找全部的 PNG 文件并将其转换为 JPG
find . -name '*.png' -exec convert {} {}.jpg \;
```

### 查找代码

- `grep` 对输入文本进行匹配。
    - `grep -C 5` 输出匹配结果前后五行。
- `ripgrep` TBD


