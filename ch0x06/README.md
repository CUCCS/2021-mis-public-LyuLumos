## 实验环境

- Android Studio 4.1
- AVD: Android 10.0 API 29 x86

## ADB实验
### 命令行
```bash
# 查看开启的模拟器
adb devices

# 连接模拟器终端
adb -s emulator-5554 shell

# 输出环境变量
echo $PATH

# 查看系统版本，lsb_release -a 不可用
uname -a

# 查看当前目录下文件
ls

# 查看防火墙规则
iptables -nL
```

![](imgs/adb.png)

```bash
# 将文件复制到设备/从设备复制文件
adb pull remote local
adb push local remote
```

![](imgs/AdbPull.png)

![](imgs/AdbPush.png)

```bash
# 安装应用
adb install path_to_apk
```

![](imgs/AdbInstall.png)

![](imgs/AdbInstallPhone.png)

### Activity Manager (am)

```bash
# Camera（照相机）的启动方法为:
am start -n com.android.camera/com.android.camera.Camera

# Browser（浏览器）的启动方法为：
am start -n com.android.browser/com.android.browser.BrowserActivity

# 启动浏览器 :
am start -a android.intent.action.VIEW -d  http://sec.cuc.edu.cn/

# 拨打电话 :
am start -a android.intent.action.CALL -d tel:10086

# 发短信：
adb shell am start -a android.intent.action.SENDTO -d sms:10086 --es sms_body ALOHA! --ez exit_on_sent true
```

![](imgs/AM.gif)

[备用链接](https://gitee.com/lyulumos/Image-Hosting-Site/blob/master/AM.gif)

### 软件包管理器 (pm)

```bash
# 语法为 pm command，这里我们随便使用两个例子

# 查看第三方软件包
pm list packages -3

# 卸载指定软件包
pm uninstall PACKAGE_NAME
```

![](imgs/AdbPm.png)

### 其他adb实验

借助之前的短信和课本给的提示命令，可以实现注释所示效果。

```bash
# 初始状态：AM的发送短信部分

# 回车按下，即换行
adb shell input keyevent 66
# 输入文本
adb shell input text ahahahah
# 焦点去到发送键
adb shell input keyevent 22
# 物理返回键，此处对应效果为 退出键盘输入
adb shell input keyevent 4
# 物理HOME键
adb shell input keyevent 3
```

![](imgs/OtherAdb.gif)

[备用链接](https://gitee.com/lyulumos/Image-Hosting-Site/blob/master/OtherAdb.gif)

## 构建第一个 Android 应用

根据 [Android - FirstApp](https://developer.android.google.cn/training/basics/firstapp) 给出的指示一步一步进行操作。代码在[这里](code/MISDemo/)。

稍有不同的是，

```java
[-] EditText editText = (EditText) findViewById(R.id.editTextText);
[+] EditText editText = (EditText) findViewById(R.id.editTextTextPersonName);
```

最终效果：

![](imgs/FirstApp.gif)

[备用链接](https://gitee.com/lyulumos/Image-Hosting-Site/blob/master/FirstApp.gif)

### 要回答的问题

- [ ] 按照向导创建的工程在模拟器里运行成功的前提下，生成的APK文件在哪儿保存的？
- [ ] 使用adb shell是否可以绕过MainActivity页面直接“唤起”第二个DisplayMessageActivity页面？是否可以在直接唤起的这个DisplayMessageActivity页面上显示自定义的一段文字，比如：你好移动互联网安全
- [ ] 如何实现在真机上运行你开发的这个Hello World程序？
- [ ] 如何修改代码实现通过 `adb shell am start -a android.intent.action.VIEW -d http://sec.cuc.edu.cn/ `可以让我们的 `cuc.edu.cn.misdemo` 程序出现在“用于打开浏览器的应用程序选择列表”？
- [ ] 如何修改应用程序默认图标？
- [ ] 如何修改代码使得应用程序图标在手机主屏幕上实现隐藏？