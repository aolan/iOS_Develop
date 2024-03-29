# 蓝牙耳机编码格式

## SBC
* 最早的蓝牙编码格式，所有蓝牙设备都支持。
* 全称 Sub-band codeing，是蓝牙音频传输协议强制规定的编码格式，是个蓝牙耳机都支持。该编码方式只追求效率，是所有蓝牙编码里音质最低的，是A2DP（Advanced Audio Distribution Profile，蓝牙音频传输协议）强制规定的编码格式，所有的蓝牙设备都会支持这个格式。
* 最主要问题是比特率较低，压缩率较高，因为蓝牙传输中间设备需要转码，以mp3为例，转码过程为 mp3 -> PCM -> SBC -> PCM，每次转码都会损失细节，导致SBC的听感会比原始的MP3要差。

## AAC
* 基础编码格式，苹果设备常用。
* 全称 Advanced Audio Codeing，高级音频编码，AAC是1997由Fraunhofer IIS、杜比实验室、AT&T、Sony等公司共同开发的，是一种高压缩比的编码算法，通过减少音频文件的体积，同时保证音乐的品质，从而达到“无损传输”。
* 但是由于蓝牙传输效率的问题，压缩之后依然是有损音质。该协议被苹果广泛用于旗下产品的蓝牙传输及Apple Music 中，经过苹果不断优化，听感有所提升。

## aptX
* 入门级别的无损格式，高通芯片（安卓机大量使用）
* 由CSR公司出品，是一种基于子带ADPCM（SB-ADPCM）技术的数字音频压缩算法。现已被高通收购，并在安卓手机里面推广力度很大。
* 该协议通过对音频进行重新编码，以提升传输效率并降低延迟。APTX现在共有三种：APTX（传统APTX）、APTX HD（高品质APTX，提高码率）、APTX Low Latency（低延迟APTX，这个对玩游戏，看视频很重要）。虽然使蓝牙音质有所提升，但仍旧无法达到有线传输的效果。
* aptX的传输码率也不高，可能和前面两者差不多，但是编码更为高效，使得声音保留的细节更多，实际听感好于前面两者，aptX的宣传也是称其可以达到CD级别的听感。

## LDAC
* 索尼推出的无损格式，索尼设备常用
* 是索尼研发无线音频编码技术，于2015年在CES展上亮相。一开始为索尼旗下产品最大卖点之一，不过在2017年，索尼正式将LDAC技术开发给了Android 8.0系统，安卓8及之后系统原生态支持该协议。
* 之所以被称为黑科技，是因为LDAC打破传统蓝牙传输信道的束缚，强行拓宽信道为原来的3倍左右，这也使得蓝牙能够传输更多的音频信息，使得听感基本上可以和有线耳机媲美。
* 但实际上蓝牙耳机的通病依然存在，就是低频的下潜深度。蓝牙耳机为了提升续航，所以动圈并没有满载运行，但在音频细节方面确实已经可以媲美有线耳机。与此同时，LDAC的存在大幅提升蓝牙功耗，使得耳机续航能力有所下降，所以一般只在耳罩式耳机上使用。而且由于信道拓宽，会受部分Wi-Fi干扰，因而在兼容音质和稳定性的模式下，码率并不能达到峰值传输。但苹果和老安卓用户不支持LDAC协议。Hi-Res音频经过LDAC的传输，可以较好的还原高音质，而通过SBC传输的结果就惨不忍睹了。

## LHDC
* LHDC是继Sony LDAC之后第二个通过日本音乐协会认证Hi-Res Audio Wireless蓝牙高音质保准的方案，其支持传输24bit/96KHz的高解析度音频，最高数据传输可900Kbps，是常规SBC编码器（328Kbps）的近3倍。

## DSEE HX
* 升级有损压缩音源，带来自然音效。这个神奇的算法可以把经过蓝牙传输后的被压缩的有损音质中损失的细节进行还原。甚至有些SBC和AAC传输后的音频经过还原听感会更好。

## HWA
华为退出的无损格式，华为设备常用


# 传输码率对比

LDAC = LHDC > aptX HD > aptx > AAC > SBC

普通用户听音乐，在听力普通和设备质量普通的情况下，可以轻易分辨 APTX HD > APTX > AAC > SBC 这四个数据传输协议的音质区别。
而从 APTX 升级到 LDAC， LHDC更高码率的方案，有些情况下听不出来，原因有三点：

* 解码设备太水了，数字转模拟的过程中已经无法表现更高码率声音信号的精细度；
* 发声单元解析度不足，无法表现解码后声音上的差异；
* 人耳朵听力有差异，无法听出区别。




参考链接：https://zhuanlan.zhihu.com/p/348160491
