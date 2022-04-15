# 检查工具

我们借助第三方的检查工具，在git commit之前对代码进行检查和格式化。这里比较推荐 [spacecommander](https://github.com/square/spacecommander) 。

## 一、本地环境搭建

* 在用户根目录 `/Users/{your name}` 创建隐藏文件路径，执行 `mkdir .code-specification`。

* 克隆 `https://github.com/square/spacecommander` 项目到该目录下。

* 我们关注`spacecommander`目录下的三个shell脚本，分别是：`setup-repo.sh`、`format-objc-files.sh`、`format-objc-files-in-repo.sh`，需要给这三个脚本添加别名，方便后续使用。

* 执行 `vi .bash_profile`，然后的填入：

```
alias fset='/Users/{your name}/.code-specification/spacecommander/setup-repo.sh'
alias fo='/Users/{your name}/.code-specification/spacecommander/format-objc-files.sh'
alias fa='/Users/{your name}/.code-specification/spacecommander/format-objc-files-in-repo.sh'
```

* 然后执行 `source .bash_profie`，让快捷配置生效。

* 如果终端使用的是 `zsh` 或者 `oh-my-zsh`，需要在 `vi .zshrc` 文件末尾增加 `source .bash_profile`，重启终端，刚刚设置的快捷方式就可以使用了。


## 二、使用GitHook来检查代码格式

* 进入到 iOS 工程根目录，执行 `fset`，工程下会新增 `.clang-format` 文件的链接，该文件配置了代码格式；除此之外，`git hooks`新增了 `pre-commit`文件，在提交之前会进行代码格式检查。

* 在工程根目录下可以执行 `fa`，来对整个工程进行格式化，不过这个要慎用，它会对 `Pods`目录下的第三方库进行格式化，不推荐。

* 也可以通过 `fo {pathToFile}` 指定某个文件。


## 三、使用xcode插件来format代码

* 下载 [XcodeClangFormat.1.3.0-beta.1.zip](https://github.com/aolan/iOS_Develop/files/8494066/XcodeClangFormat.1.3.0-beta.1.zip)。

* 解压，并将程序拖拽到 `/Applications` 中。

* 双击打开运行，选择 `/Users/{your name}/.code-specification/spacecommander/.clang-format`。

<img width="712" alt="image" src="https://user-images.githubusercontent.com/6744261/163525506-de098f45-80b2-462d-8f04-8ac6545b9a19.png">

* 打开 `系统偏好设置` - `扩展` - `Xcode Source Editor`，勾选上 `clang-format`。

* 然后重启 `Xcode`，在 `Xcode` - `Editor` 最下面就会出现 `clang-format`， 支持对选中的行进行格式化，也支持对整个文件进行格式化。

* 我们可以给`Xcode`增加format快捷键，点击 `Xcode` - `Preferences` - `Key Bindings`，在`Filter`筛选框输入`clang-format`，由于`Xcode`使用了很多快捷键，想找到一个合适的快捷键不太容易，所以此处我们只对单个文件设置快捷键。

<img width="822" alt="image" src="https://user-images.githubusercontent.com/6744261/163527419-cc084ee2-9aa5-4429-a060-d7652f3f6682.png">

* 设置完毕，就可以愉快的使用起来了。

## 四、Clang-format代码格式配置

1、有一个比较好的代码规范：[nytimes](https://github.com/nytimes/objective-c-style-guide/blob/master/README_zh-Hans.md#%E7%82%B9%E8%AF%AD%E6%B3%95)

2、我自行调试出来的格式，并对格式加了注释；可以用下面的配置替换 `/Users/{your name}/.code-specification/spacecommander/.clang-format`即可：
```
---
# 参考：https://clang.llvm.org/docs/ClangFormatStyleOptions.html

# 基于样式
BasedOnStyle: LLVM

# 语言
Language: ObjC

# 代码后注释对齐
AlignTrailingComments: true

# 单行注释前的空格数
SpacesBeforeTrailingComments: 4

# 指针和引用的对齐方式
PointerAlignment: Right

# 用于缩进的列数
IndentWidth: 4

# 针对OC的block的缩进宽度
ObjCBlockIndentWidth: 4

# 针对OC的存在block多参数的情况下，对参数进行自动换行
ObjCBreakBeforeNestedBlockParam: true

# 括号后加空格（类型转换的括号后面不需要加空格）
SpaceAfterCStyleCast: false

# switch的case缩进
IndentCaseLabels: true

# OC里面，在@property后加空格
ObjCSpaceAfterProperty: true

# OC里面，在Protocol前后加空格
ObjCSpaceBeforeProtocolList: true

# 连续的空行保留几行
MaxEmptyLinesToKeep: 1

# 保留block里面的空行
KeepEmptyLinesAtTheStartOfBlocks: false

# 每行字符的限制，0表示没有限制
ColumnLimit: 0

# 包裹数组/字典取值下标的[]中添加空格
SpacesInSquareBrackets: false

# 包括判断条件的()中添加空格
SpacesInParentheses : false

# 容器类（比如字典和数组快捷方式初始化）前添加空格
SpacesInContainerLiterals: false

# 赋值运算符前加空格
SpaceBeforeAssignmentOperators: true

# 在空括号中加空格
SpaceInEmptyParentheses: false

# 在<>中间插入空格
SpacesInAngles: false

# 换行的时候对齐操作符，对于比较长的数学表达式，在换行的时候数学符号会对齐，需要配合BreakBeforeBinaryOperators使用
AlignOperands: true

# 二元运算符位置
# None：在运算符后换行，操作数对齐
# NonAssignment：在运算符前换行，运算符对齐
BreakBeforeBinaryOperators: NonAssignment

# 赋值=对齐
AlignConsecutiveAssignments: false

# 声明参数对齐
AlignConsecutiveDeclarations: false

# 允许短的函数放在同一行
# None 不合并到同一行
# Empty 只合并空函数
# Inline 仅合并在类中定义的函数。表示“空”
# All 合并所有适合单行的函数
AllowShortFunctionsOnASingleLine: Empty

# 允许将简单的语句块放到同一行
AllowShortBlocksOnASingleLine: Empty

# 允许case在同一行
AllowShortCaseLabelsOnASingleLine: false

# 允许if在同一行
AllowShortIfStatementsOnASingleLine: Never

# 允许while在同一行
AllowShortLoopsOnASingleLine: false

# 缩进函数名
IndentWrappedFunctionNames: false

# 形参 如果为false要么都在同一行，要么各有一行
BinPackParameters: false

# 实参 如果为false要么都在同一行，要么各有一行
BinPackArguments: false

# 大括号换行
BreakBeforeBraces: Custom
BraceWrapping:
    # 函数定义后面
    AfterFunction: true
    # enum定义后面
    AfterEnum: true
    # class定义后面
    AfterClass: false
    # 控制语句后面
    AfterControlStatement: false
    # 命名空间定义后面
    AfterNamespace: false
    # struct定义后面
    AfterStruct: false
    # union定义后面
    AfterUnion: false
    # catch之前
    BeforeCatch: false
---

```

3、`clang-format` 对 `Objective-c`的 `Block` 支持不是很好，可能会将 `Block` 变成很奇怪的格式，这个时候我们只需要在代码前后增加开关 `clang-format`的注释即可。

```Objc
// clang-format off
[button addTarget:self
           action:@selector(queryCalendarSchedule)
 forControlEvents:UIControlEventTouchUpInside];
// clang-format on
```

# 参考文档：

https://www.jianshu.com/p/03ea9c01ba26

https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

https://github.com/nytimes/objective-c-style-guide/blob/master/README_zh-Hans.md

https://github.com/mapbox/XcodeClangFormat
