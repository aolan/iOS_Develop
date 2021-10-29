# 安装mysql（macOS）

* 下载链接：https://downloads.mysql.com/archives/community/
* 根据macos版本选择一个sql版本，下载安装之后，对mysql进行配置。安装过程中会要求输入密码，记住这个密码。

# 配置

```shell

vim ./.bash_profile

```
在.bash_profile文件中增加配置

```shell
export PATH=$PATH:/usr/local/mysql/bin
export PATH=$PATH:/usr/local/mysql/support-files
```

然后执行
```shell
source ./.bash_profile
```


# 使用mysql

```shell

// 启动mysql服务
sudo mysql.server start

// 登录mysql，这一步需要输入密码
mysql -u root -p

// 创建数据库
create database db_name_test;

// 使用数据库
use db_name_test;

// 创建表，后续通过sql语句来操作
...

```
