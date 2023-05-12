# 创建并使用 xcframework

## 一、 创建 framework project

![image](https://github.com/aolan/iOS_Develop/assets/6744261/7fcb407a-3a80-4a60-8105-ff39f13feb2e)

## 二、添加类和方法

![image](https://github.com/aolan/iOS_Develop/assets/6744261/eb372442-df42-48a9-9b1c-ec364841ac6a)

## 三、设置项目

`Build Settings` -> `Build Library for Distribution` 设置为 YES，否则在创建 xcframework 时会报错： ` No 'swiftinterface' files found within xx.swiftmodule`

![image](https://github.com/aolan/iOS_Develop/assets/6744261/0fe7622c-20e3-440a-bf94-b71fc47505e1)


## 四、创建不同架构的归档文件

* 真机

```
xcodebuild archive -scheme Hei -configuration Release -destination 'generic/platform=iOS' -archivePath './build/Hei.framework-iphoneos.xcarchive' SKIP_INSTALL=NO BUILD_LIBRARIES_FOR_DISTRIBUTION=YES
```

* 模拟器
```
xcodebuild archive -scheme Hei -configuration Release -destination 'generic/platform=iOS Simulator' -archivePath './build/Hei.framework-iphonesimulator.xcarchive' SKIP_INSTALL=NO BUILD_LIBRARIES_FOR_DISTRIBUTION=YES
```

## 五、合并为 xcframework 文件

```
xcodebuild -create-xcframework -framework './build/Hei.framework-iphoneos.xcarchive/Products/Library/Frameworks/Hei.framework' -framework './build/Hei.framework-iphonesimulator.xcarchive/Products/Library/Frameworks/Hei.framework' -output './build/Hei.xcframework'
```

## 六：使用 xcframework 文件

![image](https://github.com/aolan/iOS_Develop/assets/6744261/941bcbc2-4424-4662-9a4e-a0b6cab23131)
![image](https://github.com/aolan/iOS_Develop/assets/6744261/c70daaf1-3e8a-48cf-bb5c-13f77ee7b477)
![image](https://github.com/aolan/iOS_Develop/assets/6744261/6bb20241-07a1-4030-bae7-17907556c942)
