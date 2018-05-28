## 持续集成实践

两种方案：

1. Xcode Server (https://www.jianshu.com/p/412ec2ffc8b9)

2. 持续集成方案: fastlane + oclint + xctest + xcov + build + match + jenkins

``` 

fastlane 		# 持续化集成的工具集，包含很多工具

oclint 			# 用于进行静态代码检查的工具

XCTest 			# xcode自带的单元测试方法

xcov    		# 生成代码覆盖率报告的工具

build 		  	# fastlane工具，用于打包ipa包

match  			# fastlane证书管理工具

jenkins 		# 持续化集成平台，用于配置自动化测试任务


```


### fastlane

#### 环境搭建

```rb

   sudo gem install fastlane

   sudo gem install xcov

   sudo gem install xcpretty

   brew install oclint

```

#### 证书相关

##### Apple账号

可以分为三种账号：

1. 个人账号，注册时不需要提供公司信息，可以发布应用到appstore。如果不发布到appstore，采用的是adHoc的包，只能安装到100台设备上。

2. 公司账号，可以发布应用到appstore。如果不发布到appstore，采用的是adHoc的包，只能安装到100台设备上。

3. 企业账号，不可以发布应用到appstore。可以打成adHoc包，安装到100台设备上；也可以打成in-house包，不限制设备数量，这种包不建议发布到越狱市场。


##### 证书描述

1. `Development` 证书，主要用于调试设备。

2. 对于公司账号，`Production` 证书，主要用于应用分发。在做应用分发时，根据`profiles`的不同，可以用于分发成`adHoc`或者`appstore`。`adHoc`只可以安装在有限台设备上，`appstore`只可以提交到`appstore`。

3. 对于企业账号，`Production` 证书，主要用于应用分发。在做应用分发时，根据`profiles`的不同，可以用于分发成`adHoc`或者`in-house`。`adHoc`只可以安装在有限台设备上，`in-house`可以安装在无数台设备上。


##### 创建证书

1. gitlab上创建私有仓库，并将电脑上生成的ssh-key拷贝到gitlab上

2. 执行 `fastlane match development`，提示输入git链接，这里我们采用ssh链接

3. 提示输入AppId

4. 提示输入 `bundle identifier`

5. 提示输入p12的密码，如果执行成功，则在git上提交一笔记录，里面包含了certificate、profile和p12文件

6. 创建其他证书的命令分别是

```
fastlane match development

fastlane match appstore

fastlane match adhoc

fastlane match enterprise

```

##### 获取证书

```rb

match(
   git_url: "证书存放的git仓库链接，不要使用https协议，采用ssh",
   type: "appstore",
   app_identifier: "应用程序bundle identifier",
   readonly: true
)

match(
   git_url: "证书存放的git仓库链接，不要使用https协议，采用ssh",
   type: "adhoc",
   app_identifier: "应用程序bundle identifier",
   readonly: true
)

match(
   git_url: "证书存放的git仓库链接，不要使用https协议，采用ssh",
   type: "development",
   app_identifier: "应用程序bundle identifier",
   readonly: true
)

match(
   git_url: "证书存放的git仓库链接，不要使用https协议，采用ssh",
   type: "enterprise",
   app_identifier: "应用程序bundle identifier",
   readonly: true
)

```

#### 静态代码检查

``` rb

sh("xcodebuild clean -workspace testCI.xcworkspace -scheme testCI -configuration Release")
sh("xcodebuild analyze -workspace testCI.xcworkspace -scheme testCI -configuration Release | xcpretty -r json-compilation-database -o ./compile_commands.json")


oclint(
    compile_commands: "./compile_commands.json",
    exclude_regex:/Pod/,
    max_priority_1:10000,
    max_priority_2:10000,
    max_priority_3:10000,
    report_path:"reports/lint_result/total_result.html",
    report_type:"html",
    enable_clang_static_analyzer:false,
    list_enabled_rules:true,
    thresholds:[
      "LONG_CLASS=2000",          # 每个类最大行数
      "LONG_LINE=200",            # 每行字节数数量
      "LONG_METHOD=80",           # 每个方法行数
      "LONG_VARIABLE_NAME=60",    # 变量名字最长字节
      "NESTED_BLOCK_DEPTH=5",     # 嵌套深度
      "TOO_MANY_FIELDS=20",       # 字段数量
      "TOO_MANY_METHODS=30",      # 方法数量
      "TOO_MANY_PARAMETERS=6"     # 方法参数
    ],
    disable_rules:[
      "ShortVariableName",
      "CollapsibleIfStatements",
      "GotoStatement",
      "EmptyTryStatement",
      "EmptyCatchStatement",
      "EmptyIfStatement",
      "UnusedMethodParameter",
      "HighCyclomaticComplexity", # 圈复杂度
      "PreferEarlyExit"
    ]
  )

```

#### 单元测试

``` rb

run_tests(
	workspace: "testCI.xcworkspace",
  	scheme: "testCI-iosTests",
  	devices: ["iPhone 8 Plus"],
    clean: true,
    skip_build: true,
    code_coverage: true,
    output_directory: "./reports/unit_test",
    output_types: "html",
    fail_build: false
)

```


#### 代码覆盖率

``` rb

xcov(
  workspace:"testCI.xcworkspace",
  scheme:"testCI-iosTests",
  ignore_file_path: "./.xcovignore",
  output_directory: "./reports/code_coverage"
)	

```



#### 打包


``` rb

build_app(

	workspace:"#{workspace}",
    scheme:"#{target}",
    configuration:"Release",
    archive_path:"./archives/#{archive_name}",
    output_directory:"./ipa/#{ipa_path}",
    include_bitcode:true,
    clean:true,
    export_options:{
      method: "#{method}",
      provisioningProfiles:{ 
        "#{appId}" => "#{profile_name}"
      }
    }
) 


```



### jenkins

参考资料：

[MacOS安装Jenkins](https://www.jianshu.com/p/9dc3b45fbbec)

[Jenkins整合XCode详解](http://www.pluto-y.com/jenkins-xcode-configuration/)


