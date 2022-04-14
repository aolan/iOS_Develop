# 检查工具

我们借助第三方的检查工具，在git commit之前对代码进行检查和格式化。这里比较推荐 https://github.com/square/spacecommander

## 本地环境搭建

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




参考文档：
https://www.jianshu.com/p/03ea9c01ba26
