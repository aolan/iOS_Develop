# 安装ipa工具

```
brew uninstall ideviceinstaller

brew install ideviceinstaller

brew link --overwrite ideviceinstaller


```

```
brew uninstall libimobiledevice

brew install --HEAD libimobiledevice

brew link --overwrite libimobiledevice

```



# 安装ipa到真机

```
ideviceinstaller -i app.ipa
```


# 获取设备的UDID

```
idevice_id -l
```

# 查看设备上已经安装的应用的bundleId

```
ideviceinstaller -l
```

# 卸载ipa包，注意是大写U

```
ideviceinstaller -U com.company.identifier
```


