# Android 缺陷应用漏洞攻击实验
- [Android 缺陷应用漏洞攻击实验](#android-缺陷应用漏洞攻击实验)
  - [实验目的](#实验目的)
  - [实验环境](#实验环境)
  - [实验要求](#实验要求)
## 实验目的
- 理解 Android 经典的组件安全和数据安全相关代码缺陷原理和漏洞利用方法；
- 掌握 Android 模拟器运行环境搭建和 ADB 使用；
## 实验环境
- Android-InsecureBankv2
## 实验要求
详细记录实验环境搭建过程；
至少完成以下 实验 ：
- [ ] Developer Backdoor
- [ ] Insecure Logging
- [ ] Android Application patching + Weak Auth
- [ ] Exploiting Android Broadcast Receivers
- [ ] Exploiting Android Content Provider
- [ ] （可选）使用不同于 Walkthroughs 中提供的工具或方法达到相同的漏洞利用攻击效果；
推荐 drozer

<!-- ## 实验流程
### 搭建InsecureBankv2环境


使用的代码来自于 [c4pr1c3 - GitHub - Android-InsecureBankv2](https://github.com/c4pr1c3/Android-InsecureBankv2)。

首先安装所需要的包

```py
pip install -r requirements.txt
```

之后根据repo上的说明，需要运行 the AndroLab server。这里不难发现文档给定的版本是python2的，顺手改了一个[python3的版本](https://github.com/c4pr1c3/Android-InsecureBankv2/pull/2)（然后黄大貌似并没有回复我的PR /(ㄒoㄒ)/~~）并运行。


```py
# 使用默认端口8888
python app.py --host 192.168.59.1
```


这个时候只需要把安装包下载到AVD上，然后就可以愉快地开始使用了。
```bash
adb install InsecureBankv2.apk
```
![](imgs/Install.png)

检测一下是否可以正常通信。默认的账号和密码是

```
jack/Jack@123$ 
dinesh/Dinesh@123$
```

![](imgs/Login.png)

### 预处理

这里我遵从[参考三](#参考)的建议使用Android Killer，[下文](#关于android-killer)会简单介绍这个软件。

![](imgs/AndroidKillerOverview.png)


我们可以从日志文件中看出来，预处理进行的操作是，对apk文件进行了反编译（apktool）和对.dex文件转换成.class文件（dex2jar）。而图片右侧实现了将smali代码编译成java代码（jd-gui）。
## 其他

### 关于Android Killer

> Android Killer集合了之前讲过的Apktool、dex2jar、jd-gui、signapk、adb logcat等一系列工具，是目前笔者所使用过的Dalvik静态逆向平台中功能最全的一款。它在后续版本中将添加断点调试BakSmali代码的功能，并且完全免费，不足之处在于它是闭源软件，并且只支持Windows系统，使用Linux或Mac的读者可能需要寻找其他替代软件。
——《CTF特训营：技术详解、解题方法与竞赛技巧》

由于代码是完全闭源而且无法确定是不是官方来源，所以我在Windows沙盒中进行了试运行，凭借本人目前浅薄的知识，软件中暂未发现恶意代码。

![](imgs/CheckAKiller.png)

另外请注意，微软官网给出了沙盒运行的[必备条件](https://docs.microsoft.com/zh-cn/windows/security/threat-protection/windows-sandbox/windows-sandbox-overview#prerequisites)。

## 参考

- [c4pr1c3 - GitHub - Android-InsecureBankv2](https://github.com/c4pr1c3/Android-InsecureBankv2)
- [Hacktivities - InfoSec Write-ups - Android InsecureBankv2 Walkthrough: Part 1](https://infosecwriteups.com/android-insecurebankv2-walkthrough-part-1-9e0788ba5552)
- [FlappyPig - CTF特训营：技术详解、解题方法与竞赛技巧 - 第五篇：CTF之APK](https://book.douban.com/subject/35120456/) -->