# 一、搞定 Swift package manager 

> 总所周知，国内访问`github`并不稳定，代理工具并不能让`xcode`中自带的`SourceControl`走代理，来拉取`spm`三方库。

查了很多资料，其中一种方案是，通过`Proxifier`工具，进行相关配置，让`xcode`中的`SourceControl`强制走代理，但是一打开这个工具，往往电脑就无法正常访问网络了，所以最好的方式是，在命令行工具中通过命令来拉取`SPM`包。

话不多说，直接上步骤：

## 1. 给命令行设置代理

打开文件`~/.zshrc`，文件中增加代理设置。

`export all_proxy="http://127.0.0.1:1087"`

## 2. 打开Xcode
用`Xcode`打开对应的项目。

## 3. 重新拉取SPM仓库

在项目根目录执行如下命令，就可以拉取SPM包。

`xcodebuild -resolvePackageDependencies -scmProvider system`


# 二、额外知识

问：如果我们的项目中，有部分`spm`包放在`github`上，属于公司私有组的代码，直接用`spm`是拉不下来的，那么怎么做呢？

答：配置好`ssh key`，然后在本地的 `~/.gitconfig`配置文件中增加如下配置，目的在于将`https`协议用`ssh`协议替代。

```
[url "git@github.com:yourcompanygroup"]
	insteadOf = https://github.com/yourcompanygroup
```
