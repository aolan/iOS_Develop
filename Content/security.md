# iOS 安全


## 本地数据攻与防

### 一、Documents目录

存储在`Documents`目录的数据会被备份到iTunes，对于Documents目录下的敏感信息需要做加密。

### 二、Tmp目录

`Tmp`保存应⽤运行时所需的临时数据,使⽤完毕后再将相应的文件从该目录删除。

### 三、Caches目录

`Caches`一般用于保存需要持久化的数据,iTunes同步设备时不会备份该目录。一般存储体积大、不需要备份的非重要数据，比如网络数据缓存存储到下。

### 四、sqlite数据库

数据库`sqlite`是存储在`Documents`目录下的，有两种方案：如果敏感数据较少，对某些字段进行加密后进行存储；如果敏感数据较多，使用时解密会影响效率，则采用整库加密，可以采用SQLCipher。使用方法参见https://github.com/tianjifou/CoreSQLite3

```

/// SQLITE_PASSWORD为数据库密码
sqlite3_key(db, SQLITE_PASSWORD.cString(using: String.Encoding.utf8), Int32(SQLITE_PASSWORD.count));

```

### 五、Keychain

`Keychain`是一个拥有有限访问权限的SQLite数据库（AES256加密），在iPhone上，`Keychain`所存储的数据在 `/private/var/Keychains/keychain-2.db`数据库中，Keychain的数据库内容使用了设备唯一的硬件密钥进行加密，该硬件密钥无法从设备上导出。因此，存储在Keychain中的数据只能在该台设备上读取，而无法复制到另一台设备上解密后读取。一旦攻击者能够物理接触到没有设置密码的iOS设备时，他就可以通过越狱该设备，运行如`keychain_dumper`这样的工具，读取到设备所有的`Keychain`条目，获取里面存储的明文信息。具体方法是通过ssl让mac连接iPhone，使用keychain_dumper，导出Keychain。具体步骤如下：

1. 将手机越狱，通过Cydia（越狱手机都有，相当于App Store）安装OpenSSH

2. 在mac终端输入： ssh root@（手机IP） 然后会提示输入密码，默认为alpine

3. 使keychain数据库权限可读： `chmod +r keychain-2.db`

4. 下载工具Keychain-Dumper （https://github.com/ptoomey3/Keychain-Dumper）

5. 将下载的keychain_dumper可执行文件移到iPhone的/bin目录下

 * `Ctrl+D`，退出当前ssh连接

 * 输入命令：`scp /Users/ice/Downloads/Keychain-Dumper-master/keychain_dumper root@（手机ip）:/bin/keychain_dumper`

6. 添加执行权限: `chmod +x /bin/keychain_dumper`

7. 解密keychain：`/bin/keychain_dumper`，就可以输出明文信息

综上所述，Keychain相对比较安全，但是敏感信息最好还是要加密之后放到Keychain中存储。


### 六、剪切板的缓存

分为`系统级别的剪切板`和`应用内的剪切板`，如果不想把APP的数据带出去，在退出应用之前，清空剪切板的内容。


### 七、系统键盘缓存

> 我们打字在使用系统键盘打字时时，会出现提示，键盘一般会缓存这些提示字符串，缓存在手机的 
`/private/var/mobile/Library/Keyboard/dynamic-text.dat`文件中，攻击者可以提取出来，出现频率最高的往往是账户名和密码。

解决方案：

1. 禁用了自动纠正功能的文本框会阻止输入内容被缓存。设置UITextView/UITextField 属性autocorrectionType为.no类型 

2. 非常短的输入，如只有1或者2个字母组成的单词不会被缓存

3. 输入框被标记为密码输入类型，也不会被缓存。 设置UITextView/UITextField 属性isSecureTextEntry为true。

4. iOS 5之后，输入只包括数字的字符串不会被缓存。设置UITextView/UITextField属性 keyboardType为数字键盘。

5. 应用内自定义键盘，银行类APP大多使用自定义键盘。

> 第三方键盘权限问题

第三方键盘可能会收集自己的信息以及上传自己的信息，因此在苹果设置里禁止这种行为至关重要，再有就是，因为第三方键盘不可控因素很多，可能缓存数据逻辑与系统键盘不一样，防止攻击者攻击本地缓存数据，因此建议尽量少用第三方键盘。

### 八、应用进入后台时的截屏行为

> 当应用进入后台时，系统会自动在当前应用的页面截屏并存储到手机内，如果当前页面涉及敏感信息时，被攻击会造成泄密。

解决方案: 一般我们在应用被挂起时，在当前页面添加一层高斯模糊，在应用重新进入前台时，删除模糊效果。

``` swift

func applicationDidEnterBackground(_ application: UIApplication)
{
	bar = UIToolBar.init(frame: UIScreen.main.bounds);
	bar?.barStyle = .default
	self.window?.addSubView(bar!);
}

func applicationDidBecomeActive:(_ application: UIApplication)
{
	bar?.removeFromSuperView();
	bar = nil;
}


```


## HTTPS问题

HTTPS = HTTP + SSL/TLS 	(Secure Sockets Layer / Transport Layer Security)

### 原理：

1. 客户端将自己支持的一套加密规则发送给服务端。

2. 服务端从中选出一组加密算法与HASH算法，并将自己的身份信息以证书的形式发回给客户端。证书里面包含了`服务端地址`，`加密公钥`，以及`证书的颁发机构`等信息。

3. 客户端获得服务端证书之后客户端要做以下工作:

* 验证证书的合法性（颁发证书的机构是否合法，证书中包含的服务端地址是否与正在访问的地址一致等）。

* 如果证书受信任，或者是用户接受了不受信的证书，客户端会生成一串随机数的密码，并用证书中提供的公钥加密。

* 使用约定好的HASH算法计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给服务端。

4. 服务端接收客户端发来的数据之后要做以下的操作：

* 使用自己的私钥将信息解密取出密码，使用密码解密客户端发来的握手消息，并验证HASH是否与客户端发来的一致。

* 使用密码加密一段握手消息，发送给客户端。

5. 客户端解密并计算握手消息的HASH，如果与服务端发来的HASH一致，此时握手过程结束，之后所有的通信数据将由之前客户端生成的随机密码并利用对称加密算法进行加密。

### HTTPS存在的漏洞

1. SSL证书伪造

> 首先通过ARP欺骗、DNS劫持甚至网关劫持等等，将客户端的访问重定向到攻击者的机器，让客户端机器与攻击者机器建立HTTPS连接（使用伪造证书），而攻击者机器再跟服务端连接，这样用户在客户端看到的是相同域名的网站。

解决方案： 用户只要做了证书验证就能避免被劫持的，客户端会提示证书不可信。

2. SSL剥离攻击

> 该攻击方式主要是利用用户并不会每次都直接请求”https://”,来访问网站，或者有些网站并非全网HTTPS，而是只在需要进行敏感数据传输时才使用HTTPS的漏洞。中间人攻击者在劫持了客户端与服务端的HTTP会话后，将HTTP页面里面所有的”https://”超链接都换成”http://”，用户在点击相应的链接时，是使用HTTP协议来进行访问；这样，就算服务器对相应的URL只支持HTTPS链接，但中间人一样可以和服务建立HTTPS连接之后，将数据使用HTTP协议转发给客户端，实现会话劫持。

解决方案：全网都是HTTPS，不要使用HTTP的页面。

3. Charles等第三方抓包工具

> 抓包工具可以通过截获真实客户端的HTTPS请求，伪装客户端向真实服务端发送HTTPS请求和接受真实服务器响应，用自己的证书伪装服务端向真实客户端发送数据内容，我们将私有CA签发的数字证书安装到手机中并且作为受信任证书保存。

解决方案：

`AFNetworking中`安全策略`AFSSLPinningMode`设置为`AFSSLPinningModeCertificate`。代表客户端会将服务器端返回的证书和本地保存的证书中的所有内容，包括PublicKey和证书部分，全部进行校验；如果正确，才继续进行。这种方式可以避免中间人攻击。本地保存的证书，最好的做法是，在代码中存放证书的二进制流数据。


## UIWebView漏洞

1. UIWebView XSS漏洞(跨站脚本攻击`Cross Site Scripting`)

> 1. 未知的来源的进入了web页面，如对用户输入框输入的数据没有进行过滤，恶意数据通过用户组件，URL scheme handlers或者notifications传递给了UIWebView组件，并且UIWebView组件没有验证该数据的合法性。

> 2. UIWebView通过loadHTMLString:baseURL：这个方法从本地html读取代码，然后加载，而这个HTML代码并没有被信任的未知来源，这个恶意的本地HTML可以读取所有的保存在该应用里的cookie，绕过同源策略做一些非法的操作，攻击者将用户的像cookie，session等敏感数据传递给攻击者，或者重定向用户页面到攻击者控制的服务器上，或者诱导用户输入一些敏感数据，然后传递到攻击者的服务器上，实现钓鱼攻击。

解决方案：1. 对输入框内的数据进行关键词过滤（如“script”字符串），避免输入恶意脚本语言。2. 将HTML加载成字符串，替换字符串中的script。3.可以通过这些截断的URL来做一些白名单、黑名单策略，如禁用file域，不让它加载本地文件。

2. UIWebView https中间人攻击漏洞

解决方案：借助AFNetworking验证URL，加载HTML数据，然后再用webView加载AFNetworking下载的数据。


## 逆向工程反编译APP

市场上有很多反编译工具如class-dump-z、IDA、Hopper Disassembler，他们通常是和Clutch一起使用。因为APP Store上的APP都是加密过的，需要先解密，解密一般可以得到资源文件、二进制文件， Clutch 就是做这个工作的软件。class-dump-z ，一般用来反编译oc工程，不支持swift，IDA收费，而且太贵，我们主要使用Hopper来反编译项目。只要将项目的二进制文件拖入hopper内即可反编译。


### 字符串加密

> 例如提示给用户的toast信息，可以很方便的通过toast信息去代码中查询该字符串，轻而易举的找到该段代码

解决方案: 代码中应该尽量减少这种写法，将提示信息加密后放置在代码中。使用的时候通过解密密文展示到toast上。


### 类名方法名混淆

> 通过`class-dump`可以把应用的类和方法定义dump下来

解决方案：对于程序中的类名方法名，自己产生一个随机的字符串来替换这些定义的类名和方法名，但是不是所有类名，方法名都能替换的，要过滤到系统有关的函数以及类，可以参考下开源项目：https://github.com/Polidea/ios-class-guard
 

### 程序代码混淆

> iOS应用的可执行文件拖到`Hopper Disassembler`或者`IDA`里面，程序逻辑一目了然。

解决方案：可以基于Xcode使用的编译器clang，然后在中间层也就是IR实现自己的一些混淆处理，比如加入一些无用的逻辑块啊，代码块啊，以及加入各种跳转但是又不影响程序原有的逻辑。可以参考下开源项目：https://github.com/obfuscator-llvm/obfuscator/  当然开源项目中也是存在一些问题的，还需自己再去做一些优化工作。

### 加入安全SDK

> 目前大多数IOS应用对于简单的反调试功能都没有，更别说注入检测，以及其它的一些检测了。(需要看看怎么调试APP)

解决方案：加入SDK，包括多处调试检测，注入检测，越狱检测，关键代码加密，防篡改等等功能。并提供接口给开发者处理检测结果。


## Cycript脚本语言显示和修改APP的UI布局与代码逻辑

Cycript是由Cydia创始人Saurik推出的一款脚本语言,支持oc和js语言的编辑器。通过Cycript下载安装可以直接使用。Cycript 语法使用文档: http://iphonedevwiki.net/index.php/Cycript_Tricks

Cycript可以做到：

1. 创建对象

2. 输出当前APP页面布局 `UIApp.keyWindow.recursiveDescription().toString()`

3. 添加方法

4. 更改对象属性

5. Load frameworks

如何使用Cycript：

1. 通过Cydia下载安装Cycript

2. 通过ssh连接手机和电脑，在mac终端输入： `ssh root@（手机IP）` 然后会提示输入密码，默认为alpine

3. 输出所要更改应用的当前进程地址和文件地址：`ps aux | grep demo名（不一定是APP名）`

4. 使用cycript连接这个app： `cycript -p 6542 `，其中`6542`是APP的进程号

5. 控制台输入`UIApp.keyWindow.recursiveDescription().toString()`，就可以打印页面布局了


## 检测越狱与反调试

### 检测越狱

1. 常见越狱存在的文件

>  /Applications/Cydia.app
>  /Library/MobileSubstrate/MobileSubstrate.dylib
>  /bin/bash
>  /usr/sbin/sshd
>  /etc/apt

在下面代码中输入上述路径，判断是否存在，存在表示该设备已被越狱

``` swift 

func existFile(path: String)->Bool
{
	return FileManager.default.fileExists(atPath: path);
}


```


2. 判断cydia的URL scheme

一般越狱的设备中都会安装cydia这个应用，我们可以通过是否可以打开这个应用检测设备是否越狱。


``` swift 

func openUrlScheme()->Bool
{
	return UIApplication.shared.canOpenURL(URL.init(string: "cydia://")!);
}

```

### 反调试

## 扫描工具对代码的扫描

市面上有很多扫描工具，一般比较流行的有fortify和coverity，其中coverity对代码的检查侧重于代码质量，fortify侧重于代码的安全漏洞。我们这里介绍的是使用fortify对代码的漏洞分析。






参考链接：http://www.blogfshare.com/ios-protect.html
参考链接：http://blog.csdn.net/tianjifou/article/details/77963252
HTTPS参考：http://www.jianshu.com/p/4102b817ff2f