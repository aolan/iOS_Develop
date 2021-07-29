# Git Config 正确配置


我们常常未无法访问github而感到苦恼，尤其是在结合cocoapods之后，更是如此。

解决方案：在用户根目录下有一个文件 .gitconfig，该文件为git配置文件。

* 可查看git全局配置 `git config -l --global`

* 设置git代理 `git config --global http.https://github.com.proxy socks://127.0.0.1:1069`

* 设置sslVerify，必须要设置true  `git config --global http.sslVerify true`

* 设置 postBuffer，对于仓库较大的项目，需要将缓存区设置打一些 `git config --global http.postBuffer 4194304000`



## 坑

1. 不知道什么时候，我的git config中有一段这样的配置`remote.origin.proxy=`，这个配置导致我无法拉取github上的应用，删除即可
