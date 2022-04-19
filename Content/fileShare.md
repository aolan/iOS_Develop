# 文件共享的几种方式

## 一、Documents目录共享

1、在 info.plist 文件中添加两组key，在系统自带的File App中就可以看到；

```
LSSupportsOpeningDocumentsInPlace 设置为 YES
UIFileSharingEnabled 设置为 YES
```
2、然后在 File App 中操作文件，将目标文件移动到自己的APP中；文件也就相应的被移动Documents目录下。


## 二、读取 File App中的文件

```objc

#import <UIKit/UIKit.h>

- (void)pickerFiles
{
    NSArray *documentTypes = @[
        @"public.text",
        @"public.content",
        @"public.source-code",
        @"public.image",
        @"public.audiovisual-content",
        @"com.adobe.pdf",
        @"com.apple.keynote.key",
        @"com.microsoft.word.doc",
        @"com.microsoft.excel.xls",
        @"com.microsoft.powerpoint.ppt"
    ];
    UIDocumentPickerViewController *vc = [[UIDocumentPickerViewController alloc] initWithDocumentTypes:documentTypes inMode:UIDocumentPickerModeImport];
    vc.delegate = self;
    if (@available(iOS 11.0, *)) {
        vc.allowsMultipleSelection = YES;
    }
    AppDelegate *delegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
    UINavigationController *navCtrl = (UINavigationController *)delegate.window.rootViewController;
    [navCtrl presentViewController:vc animated:YES completion:NULL];
}

#pragma mark - UIDocumentPickerDelegate && UIDocumentInteractionControllerDelegate

// [iOS8, iOS11)
- (void)documentPicker:(UIDocumentPickerViewController *)controller didPickDocumentAtURL:(NSURL *)url
{
    NSLog(@"url = %@", url);
}

// [iOS11, now]
- (void)documentPicker:(UIDocumentPickerViewController *)controller didPickDocumentsAtURLs:(NSArray<NSURL *> *)urls
{
    NSLog(@"urls = %@", urls);
    if (urls.count) {
        NSURL *url = [urls objectAtIndex:0];
        NSData *data = [NSData dataWithContentsOfURL:url];
        NSLog(@"data.length: %zd", data.length);
    }
}

- (void)documentPickerWasCancelled:(UIDocumentPickerViewController *)controller
{
    NSLog(@"canceled");
}

```

## 三、添加支持的文件类型（UTI）

1、info.plist 文件中配置支持的文件类型 `CFBundleDocumentTypes`，配置方案如下图：

<img width="909" alt="image" src="https://user-images.githubusercontent.com/6744261/163978004-214fed4f-deb8-4c08-88aa-e7f098fb103b.png">

2、这里配置的时候，可能是xcode13.2的bug，icon无法添加，可以通过 sourceCode 方式打开 plist 文件进行添加，其中有两个icon不是正方形，比如 icon_22x29 ，指的是宽度为22像素，高度是29像素。

```
<key>CFBundleDocumentTypes</key>
	<array>
		<dict>
			<key>CFBundleTypeIconFiles</key>
			<array>
				<string>icon_22x29</string>
				<string>icon_44x58</string>
				<string>icon_64x64</string>
				<string>icon_320x320</string>
			</array>
			<key>CFBundleTypeName</key>
			<string>public.image</string>
			<key>LSHandlerRank</key>
			<string>Owner</string>
			<key>LSItemContentTypes</key>
			<array>
				<string>public.image</string>
			</array>
		</dict>
	</array>
```

3、然后在 AppDelegate 方法中处理外部分享过来的文件，即可

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey, id> *)options
{
    NSLog(@"url: %@", url);
    NSLog(@"info: %@", options);
    return YES;
}
```

4、通过 File App 分享的截图如下：

<img width="440" alt="image" src="https://user-images.githubusercontent.com/6744261/163979396-0ac8b6db-5327-4ba2-aac7-da008e011efe.png">


5、通过 File App 分享过来的图片，我们看到日志如下：

<img width="739" alt="image" src="https://user-images.githubusercontent.com/6744261/163979124-a88bf5a0-dab5-4790-8ad9-2694850475eb.png">




