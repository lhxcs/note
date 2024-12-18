# 元编程

## 构建系统

我们需要工具来帮助我们完成一个“构建过程”。我们需要定义**依赖，目标和规则**。我们告诉构建系统我们具体的构建目标，系统的任务是找到构建这些目标所需要的依赖，并根据规则构建所需的中间产物，直到最终目标被构建出来。如果目标的依赖没有发生改动，并且我们可以从之前的构建中复用这些依赖，那么与其相关的构建规则就不会被执行。

`make`.

## 依赖管理

- 大多数的依赖可以通过某些软件仓库来获取，这些仓库会在一个地方托管大量的依赖，我们可以通过一套非常简单的机制来安装依赖。（例如 ubuntu 的 ubuntu 软件包仓库，我们可以通过 apt 工具来访问。）
- **版本控制**
    - 版本号最重要的作用是保证软件能够运行。假如某个库要发布一个新版本，在这个版本里面重命名了某个函数。如果有人在库升级后仍希望用它构建新的软件，这时候就可能构建失败。版本控制可以很好解决这个问题，我们可以指定当前项目需要基于某个版本，甚至某个范围内变换的版本。
    - 但是如果我们发布了一项和安全相关的升级，它没有影响到 API, 但是出于安全到考虑，依赖它的项目都应该立即升级。这时候我们就可以引入版本号的具体构成：
    - 版本号的格式：主版本号.次版本号.补丁号
        - 如果新的版本没有改变 API, 将补丁号递增。
        - 如果添加了 API 并且该改动是向后兼容的，将次版本号递增。
        - 如果修改了 API 但是不向后兼容，将主版本号递增。

## 持续集成系统

随着项目规模越来越大，我们会发现除了修改代码之后还有很多额外的工作要做。例如我们希望每次有人提交代码到 github 的时候他们的代码风格被检查过并执行过某些基准测试。这时候我们就引入**持续集成**的概念。

持续集成 (Continuous integraion) (CI) 指的是那些当我们的代码变动时，自动运行的东西。
它们的工作原理是，我们需要在代码仓库中添加一个文件，描述当前仓库发生任何修改时，应该如何应对。