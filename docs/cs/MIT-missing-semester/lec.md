# Shell

shell 是一个编程环境，它具备变量、条件、循环和函数。当在 shell 中执行命令时，实际上是在执行一段 shell 可以解释执行的简短代码。

## 在 shell 中导航

- 路径 `/` 代表的是系统的根目录，所有的文件夹都包括在这个路径之下。
- 当前工作目录可以使用 `pwd` 来获取。
- `ls -l` 可以更加详细地列出目录下文件或文件夹的信息。
    - 第一个字符 `d` 表示 `missing` 是一个目录。
    - 接下来的九个字符，每三个字符构成一组。它们分别代表了文件所有者，用户组以及其他所有人具有的权限。

```
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

