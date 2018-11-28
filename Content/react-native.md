## React Native

### 一、搭建环境

参考链接：https://reactnative.cn/docs/getting-started/


### 二、使用新工程显示首页

#### 1.导航栏

 1. 引入react-navigation

 2. react-navigation依赖react-native-gesture-handler

参考链接：https://reactnavigation.org/docs/en/getting-started.html

#### 2.首页广告轮播

 1. 引入 react-native-swiper


#### 3.首页横向滚动scrollView

 1. 横向滚动使用 FlatList


### 五、引入到现有工程中

 1. react-navigation不能采用官方文档中的方式引入

 2. react-navigation依赖的react-native-gesture-handler应该采用podfile来引入

```
    pod 'RNGestureHandler', :path => '../../node_modules/react-native-gesture-handler/ios'

```


### 四、打包

```
react-native bundle --entry-file index.js --bundle-output index.ios.jsbundle --platform ios --assets-dest ./ --dev false

```

参考链接：https://www.jianshu.com/p/bb7c5f1d304e



### React Native原理

 1. 程序启动，RN的各个模块都会通过```RCT_EXPORT_MODULE```宏向RN中注册模块，注册过的模块会被存放在 ```RCTModuleClasses```中。

 2. 创建RCTRootView对象，并将对象贴到window上

 3. 创建RCTBridge，桥接对象，管理JS和OC交互。在初始化方法中调用setup方法，在setup方法中创建了RCTxxBridge。

 4. 然后调用RCTxxBridge实例的start方法。start方法中创建了js的执行线程，并调用loadSource方法加载js源码，而loadSource实际是调用RCTJavaScriptLoader类来获取js文件中的内容的。最终调用JSCHelper类的evaluateScript方法来完成native和js的交互。

