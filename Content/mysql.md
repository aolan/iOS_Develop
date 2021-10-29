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

# nestjs

## 安装并初始化工程

```shell

// 安装nestjs，确保node版本大于8.9.0
npm -i -g @nestjs/cli@6.0.0

// 创建nest项目
nest new test-nestjs

```

## 项目结构介绍

src
├── app.controller.spec.ts     
├── app.controller.ts         带有单个路由的基本控制器示例：控制器负责处理传入的请求和向客户端返回响应
├── app.module.ts             应用程序的根模块
├── app.service.ts
└── main.ts                   应用程序入口文件。它使用 NestFactory 用来创建 Nest 应用实例

## 创建自己的controller

```shell
nest g controller cats
```
