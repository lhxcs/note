# Makefile

## makefile 介绍



make命令执行时，需要一个makefile文件，以告诉make命令需要怎么样的去编译和链接程序。

**告诉make命令如何编译和链接文件**

- 如果这个工程没有编译过，那么所有的c文件都要编译并被链接。
- 如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接目标程序。
- 如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。

### makefile规则

```makefile
target ... : prerequisites ...
    recipe
    ...
    ...
```



- target: 可以是一个目标文件，一个可执行文件，或者一个标签。
- prerequisites: 生成该target所依赖的文件和/或target。
- recipe：该target要执行的命令(任意的shell命令)。

即：prerequisites中如果有一个以上的文件比target文件要新的话，recipe所定义的命令就会被执行。