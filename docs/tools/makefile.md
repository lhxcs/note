# Makefile

## Makefile 的规则

```Makefile
target ... : prerequisites ...
    recipe
    ...
    ...
```

- target: 可以是一个目标文件(`.o`), 也可以是一个可执行文件，还可以是一个标签。
- prerequisites: 生成该 target 所依赖的文件和/或 target。
- recipe: 该 target 要执行的命令(任意的 shell 命令)。

prerequisites 中如果有一个以上的文件比 target 文件要新的话，recipe 所定义的命令就会被执行。

```Makefile
edit : main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
    cc -o edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o

main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit main.o kbd.o command.o display.o \
        insert.o search.o files.o utils.o
```

## make 如何工作

输入 `make ` 命令：

- make 会在当前目录下找到名字叫 `Makefile` 或 `makefile` 的文件。
- 如果找到，它会找到文件中的第一个目标文件，并把这个文件作为最终的目标文件。
- 如果不存在，或者是该文件所依赖的文件的修改时间比该文件新，那么它就会执行后面所定义的命令来生成目标文件。
- 如果目标文件所依赖的文件也不存在，make 会在当前文件中找目标为 `.o` 文件的依赖性，找到后按规则生成。

即一层层去找文件的依赖关系，直到最终编译出第一个目标文件。


## 使用变量

声明变量：

```Makefile
objects = main.o kbd.o command.o display.o \
     insert.o search.o files.o utils.o

edit : $(objects)
    cc -o edit $(objects)
main.o : main.c defs.h
    cc -c main.c
kbd.o : kbd.c defs.h command.h
    cc -c kbd.c
command.o : command.c defs.h command.h
    cc -c command.c
display.o : display.c defs.h buffer.h
    cc -c display.c
insert.o : insert.c defs.h buffer.h
    cc -c insert.c
search.o : search.c defs.h buffer.h
    cc -c search.c
files.o : files.c defs.h buffer.h command.h
    cc -c files.c
utils.o : utils.c defs.h
    cc -c utils.c
clean :
    rm edit $(objects)
```

## 让 make 自动推导

GNU 的 make 可以自动推导文件以及文件依赖关系后面的命令。

只要make看到一个 .o 文件，它就会自动的把 .c 文件加在依赖关系中，如果make找到一个 whatever.o ，那么 whatever.c 就会是 whatever.o 的依赖文件。并且 cc -c whatever.c 也会被推导出来。

## 清空目录的规则

每个Makefile中都应该写一个清空目标文件（ .o ）和可执行文件的规则，这不仅便于重编译，也很利于保持文件的清洁。

```Makefile
.PHONY: clean
clean:
    -rm edit $(objects)
```

- `.PHONY` 表示 `clean` 是一个伪目标。
- `rm` 命令前面加了一个小减号的意思就是，也许某些文件出现问题，但不要管，继续做后面的事。
- `clean` 规则不要放在文件的开头。

## Makefile 里有什么

- 显式规则：说明如何生成一个或多个目标文件。
- 隐式规则：自动推导的功能，我们可以简略书写 makefile。
- 变量的定义
- 指令
- 注释