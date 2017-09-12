# 模拟器路径变化

1、xcode5及以前的版本，模拟器路径为：/Users/用户名/Library/Application Support/iPhone Simulator

2、xcode6及以后的版本，模拟器路径为：/Users/用户名/ Library/Developer/CoreSimulator

# 几种常见的文件路径


文件路径 | 描述 |
------------ | ------------- | 
Library | 可以重新下载或者重新生成的数据应该保存在 <Application_Home>/Library/Caches 目录下面。举个例子，比如杂志、新闻、地图应用使用的数据库缓存文件和可下载内容应该保存到这个文件夹。
tmp | 只是临时使用的数据应该保存到 <Application_Home>/tmp 文件夹。尽管 iCloud 不会备份这些文件，但在应用在使用完这些数据之后要注意随时删除，避免占用用户设备的空间。
Documents | 只有用户生成的文件、其他数据及其他程序不能重新创建的文件，应该保存在<Application_Home>/Documents 目录下面，并将通过iCloud自动备份。
Library/Preferences | ​这个目录包含应用程序的偏好设置文件。使用NSUserDefaults类进行偏好设置文件的创建、读取和修改。


# 知识点

1、 NSData    【缓冲区，对于32位的应用程序，NSData缓冲区最多可存储2GB的数据，对于64位的应用程序，最多可存储8EB（8亿GB的数据）】

2、 NSFileManager

3、 尽量使用NSPathUtilities.h中提供的几种函数和方法。
