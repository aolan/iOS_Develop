# macos 外接硬盘扩展存储
> 假设我们要将 macOS 中的 ~/Downloads 目录映射到外接硬盘，并让所有应用的下载文件自动保存到外接硬盘。

## 操作步骤

### 1. 连接外接硬盘
确保外接硬盘已正确连接到 Mac，并在「访达」的侧边栏中可见（通常显示为硬盘名称，如 MyExternalDrive）。

### 2. 创建目标文件夹
在外接硬盘中创建一个用于替代原 Downloads 的文件夹，例如：
```/Volumes/MyExternalDrive/Downloads```
（路径中的 `MyExternalDrive` 替换为你的硬盘名称）。

### 3. 备份原 Downloads 内容
如果原 ~/Downloads 中有文件，先复制到外接硬盘的新文件夹中，避免数据丢失：
```bash
cp -R ~/Downloads/* /Volumes/MyExternalDrive/Downloads/
```

### 4. 删除或移动原 Downloads 目录
首先删除（或移走）系统默认的 ~/Downloads 目录（后续会用符号链接替代）：
```bash
rm -rf ~/Downloads
```
注意：如果系统提示权限不足，可在命令前加 sudo 并输入管理员密码。

### 5. 创建符号链接
用 ln -s 命令将外接硬盘的新文件夹映射到 ~/Downloads：
```bash
ln -s /Volumes/MyExternalDrive/Downloads ~/Downloads
```
这条命令的含义是：让 ~/Downloads 成为外接硬盘中 Downloads 文件夹的「快捷方式」，所有对 ~/Downloads 的操作都会实际作用于外接硬盘。

### 6. 验证设置
打开「终端」，输入 `ls -l ~/Downloads`，如果输出中包含 `-> /Volumes/MyExternalDrive/Downloads`，说明链接创建成功。
尝试下载一个文件（如浏览器下载），检查文件是否自动保存到外接硬盘的 Downloads 文件夹中。

### 7. 注意事项
外接硬盘需保持连接状态，否则应用可能因无法访问 ~/Downloads 而报错。
如果更换外接硬盘或修改硬盘名称，需重新执行步骤 4-5 以更新符号链接。
若后续想恢复默认设置，只需删除符号链接并重建系统默认的 Downloads 目录：

```bash
rm ~/Downloads
mkdir ~/Downloads
```

通过以上设置，所有应用的下载文件会自动保存到外接硬盘，达到节省 Mac 本地存储空间的目的。
