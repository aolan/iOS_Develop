# 检查工具

我们借助第三方的检查工具，在git commit之前对代码进行检查和格式化。这里比较推荐 https://github.com/square/spacecommander

## 一、本地环境搭建

* 在用户根目录 `/Users/{your name}` 创建隐藏文件路径，执行 `mkdir .code-specification`

* 克隆 https://github.com/square/spacecommander 项目到该目录下

* 我们关注`spacecommander`目录下的三个shell脚本，分别是：`setup-repo.sh`、`format-objc-files.sh`、`format-objc-files-in-repo.sh`，需要给这三个脚本添加别名，方便后续使用。

* 执行 `vi .bash_profile`，然后的填入：

> alias fset='/Users/{your name}/.code-specification/spacecommander/setup-repo.sh'
> 
> alias fo='/Users/{your name}/.code-specification/spacecommander/format-objc-files.sh'
> 
> alias fa='/Users/{your name}/.code-specification/spacecommander/format-objc-files-in-repo.sh'

* 然后执行 `source .bash_profie`，让快捷配置生效。

* 如果终端使用的是 `oh-my-zsh` 或者 `oh-my-zsh`，需要在 `vi .zshrc` 文件末尾增加 `source .bash_profile`，重启终端，刚刚设置的快捷方式就可以使用了。


## 二、使用

* 进入到 iOS 工程根目录，执行 `fset`，工程下会新增 `.clang-format` 文件的链接，该文件配置了代码格式；除此之外，`git hooks`新增了 `pre-commit`文件，在提交之前会进行代码格式检查。

* 在工程根目录下可以执行 `fa`，来对整个工程进行格式化，也可以通过 `fo {pathToFile}` 指定某个文件。




参考文档：
https://www.jianshu.com/p/03ea9c01ba26
