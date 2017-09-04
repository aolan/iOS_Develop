# 识别二维码

```

UIImage * srcImage = qrcodeImage;

CIContext *context = [CIContext contextWithOptions:nil];
CIDetector *detector = [CIDetector detectorOfType:CIDetectorTypeQRCode context:context options:@{CIDetectorAccuracy:CIDetectorAccuracyHigh}];
CIImage *image = [CIImage imageWithCGImage:srcImage.CGImage];
NSArray *features = [detector featuresInImage:image];
CIQRCodeFeature *feature = [features firstObject];

NSString *result = feature.messageString;

```

# 生成二维码

```

NSString *text = self.tfCode.text;

NSData *stringData = [text dataUsingEncoding: NSUTF8StringEncoding];

// 生成
CIFilter *qrFilter = [CIFilter filterWithName:@"CIQRCodeGenerator"];
[qrFilter setValue:stringData forKey:@"inputMessage"];
[qrFilter setValue:@"M" forKey:@"inputCorrectionLevel"];

UIColor *onColor = [UIColor redColor];
UIColor *offColor = [UIColor blueColor];

// 上色
CIFilter *colorFilter = [CIFilter filterWithName:@"CIFalseColor"
                                   keysAndValues:
                         @"inputImage",qrFilter.outputImage,
                         @"inputColor0",[CIColor colorWithCGColor:onColor.CGColor],
                         @"inputColor1",[CIColor colorWithCGColor:offColor.CGColor],
                         nil];

CIImage *qrImage = colorFilter.outputImage;

// 绘制
CGSize size = CGSizeMake(300, 300);
CGImageRef cgImage = [[CIContext contextWithOptions:nil] createCGImage:qrImage fromRect:qrImage.extent];
UIGraphicsBeginImageContext(size);
CGContextRef context = UIGraphicsGetCurrentContext();
CGContextSetInterpolationQuality(context, kCGInterpolationNone);
CGContextScaleCTM(context, 1.0, -1.0);
CGContextDrawImage(context, CGContextGetClipBoundingBox(context), cgImage);
UIImage *codeImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();

CGImageRelease(cgImage);

```


参考：http://adad184.com/2015/09/30/goodbye-zxing/
