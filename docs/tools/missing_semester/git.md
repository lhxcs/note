# 版本控制(Git)

## git 的数据模型

- 快照(snapshots)
    - git 将顶级目录中的文件和文件夹作为集合，并通过一系列快照来管理其历史记录。
    - 文件被称作 blob 对象（数据对象），也就是一组数据。
    - 目录(directory)被称作树，它将名字与 blob 对象或树对象进行映射。不是很懂。
    - a snapshot is the top-level tree that is being tracked. 

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

In the above example, the top-level tree contains two elements, a tree "foo" (that itself contains one element, a blob "bar.txt"), and a blob "baz.txt".

- Modeling history: relating snapshots
    - 在 git 中，历史记录是一个由 snapshot 组成的有向无环图。(每个 snapshots 都有一系列之前的 snapshot，某些 snapshot 可能由多个 parents 而来，例如经过合并后的两条分支)
    - 这些 snapshot 被称为 commit。
    - 可视化:
    ```
    o <-- o <-- o <-- o
            ^
             \
              --- o <-- o
    ```
    - 箭头指向当前 commit 的 parent （这里的箭头表示“在……之前”的关系）。可以看到第三次提交之后，历史记录分岔成了两条独立的分支。（这可能因为此时需要同时开发两个不同的特性，它们之间是相互独立的）。开发完成后，这些分支会被合并并创建一个新的 commit, 新的 commit 创建一个新的历史记录。

    ```
    o <-- o <-- o <-- o <----  o 
            ^            /
             \          v
              --- o <-- o
    ```

- 数据模型及其伪代码表示

```
type blob = array<byte> // 文件就是一组数据

type tree = map<string, tree | blob> // 一个包含文件和目录的目录

type commit = struct {
    parents: array<commit>
    author: string
    message: string
    snapshot: tree
}
//每一个提交都包含一个父辈，元数据和顶层树
```

- 对象和内存寻址
    - git 中的对象可以是 blob, tree 和 commit
    - `type object = blob | tree | commit`
    - git 在存储数据时，所有的对象都会基于它们的 sha-1 哈希进行寻址。

    ```
    objects = map <string, object>

    def store(object):
        id = sha1(object)
        objects[id] = object

    def load(id):
        return objects[id]
    ```

    - 当它们引用其它对象时，它们并没有真正在硬盘上保存这些对象，而是仅仅保存了它们的哈希值作为引用。
- 引用
    - 现在我们所有的 snapshot 都可以通过它们的 SHA-1 哈希值来标记。但是因为这是一个 40 位的十六进制串，很不方便。
    - 所以 git 将这些哈希值赋予人类可读的名字，也就是引用。
    - 引用是指向 commit 的指针。引用是可变的 (引用可以被更新，指向新的 commit)
    - 这样 git 就可以使用诸如 master 这样可读的名称来表示历史记录中某个特定的提交。

    ```
    references = map<string, string>

    def update_reference(name, id):
        references[name] = id

    def read_reference(name):
        return references[name]

    def load_reference(name_or_id):
        if name_or_id in references:
            return load(references[name_or_id])
        else:
            return load(name_or_id)

    ```

- 仓库
    - git 仓库的定义：对象和引用
    - 在硬盘上，git 仅存储对象和引用。所有 git 命令都对应着提交树的操作，例如增加对象，增加或删除引用。

## 暂存区

staging area: 允许我们指定下次 snapshot 中要包括哪些改动。

## 命令行接口

- `git log`： 显示历史日志
- `git log --all --graph --decorate`: 可视化历史记录(有向无环图)
- `git diff <filename>`: 显示与暂存区文件的差异。
- `git diff <revision> <filename>` 显示某个文件两个版本之间的差异
- `git checkout <revision>` 更新 head 和目前的分支。

 

