---
layout: post
title: "Mobile Security"
date: 2022-03-03
favorite: "true"
lastUpdate: 2022-03-03
---

#### 1. Client Isolation

##### Android

You can configure the proxy on your Android device to point to 127.0.0.1:8080, connect your phone via USB to your host computer and use adb to make a reverse port forwarding:

```bash
$ adb reverse tcp:8080 tcp:8080
```
Forward the data received on port 27042 on the PC to port 443 on the Android
```
$ adb forward tcp:27042 tcp:443
```

```
adb shell input text 'http://portswigger-labs.net/hackability/'
adb shell dumpsys activity top | grep "mFocusedActivity" # 查看当前处于前台的Activity
adb shell dumpsys dbinfo [packagename] # Show Applications Database Info
adb shell dumpsys package <package-name> # 查看包信息，包括四大组件信息以及MIME等相关信息
adb shell dbinfo <package-name> # 用于查看App使用的数据库信息，包括执行操作的查询语 句等信息都会被打印出来
adb shell screencap -p <path> # 截图并保存到<path>目录下
adb shell pm命令 # pm命令是Android中packageManager的命令行，是用 于管理package的命令，比如通过pm list packages命令可以列出所 有安装的APK包名
adb shell am命令 # am命令是一个重要的调试工具，主要用于启动或停止服 务、发送广播、启动Activity等。在逆向过程中，往往在需要以 Debug模式启动App时会使用这个命令
adb shell am start-activity -D -N <包名>/<类名>
```

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x05b-Basic-Security_Testing.md#client-isolation-in-wireless-networks

##### IOS

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06b-Basic-Security-Testing.md#basic-network-monitoringsniffing

First we need to use iproxy to make SSH from iOS available on localhost.

```bash
$ brew install libimobiledevice
$ iproxy 2222 22
waiting for connection
```

The next step is to make a remote port forwarding of port 8080 on the iOS device to the localhost interface on our computer to port 8080.

```
ssh -R 8080:localhost:8080 root@localhost -p 2222
```

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06b-Basic-Security-Testing.md#using-burp-via-usb-on-a-jailbroken-device

#### 2. I can't access HTTPS URLs on iOS even after installing Burp's CA certificate

1. In Burp, go to the **Proxy > Options** tab.
2. Select the proxy listener that you use for your mobile device and click **Edit**.
3. In the **Edit proxy listener** dialog, go to the **TLS Protocols** tab.
4. Select **Use custom protocols**, then deselect **TLSv1.3** from the list.

https://portswigger.net/burp/documentation/desktop/mobile/troubleshooting#i-can-t-access-https-urls-on-ios-even-after-installing-burp-s-ca-certificate

#### 3. Bypassing Certificate Pinning

Android

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x05g-Testing-Network-Communication.md#bypassing-certificate-pinning

IOS

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06g-Testing-Network-Communication.md#bypassing-certificate-pinning

#### 4. Installing Apps

IOS

```bash
### download ipa
npm install -g itms-services
itms-services -u "itms-services://?action=download-manifest&url=https://s3-ap-southeast-1.amazonaws.com/test-uat/manifest.plist" -o - > out.ipa

### dump unencrypted version ipa
# https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06b-Basic-Security-Testing.md#using-frida-ios-dump
./dump.py -l # enumerate the apps installed
./dump.py ph.telegra.Telegraph # dump ipa

## install ipa
### 方法一
$ brew install libimobiledevice 
$ brew install ideviceinstaller
$ ideviceinstaller -i iGoat-Swift_v1.0-frida-codesign.ipa # Failed to verify code signature of xxx

### 方法二
npm install -g ios-deploy
$ unzip Name.ipa 
$ ios-deploy --bundle ' Payload/Name.app ' -W -d -v
$ ios-deploy --bundle ' Payload/Name.app ' -W -d -v -m # run and debug

### Xcode
### ipainstaller https://github.com/autopear/ipainstaller
```

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x06b-Basic-Security-Testing.md#installing-apps

#### 5. Mobile App Network Communication

https://github.com/OWASP/owasp-mstg/blob/master/Document/0x04f-Testing-Network-Communication.md#mobile-app-network-communication

#### 6. IOS Reverse Engineering

```bash
# https://github.com/frida/frida/issues/2005 坑
frida-trace -U -i "CCCryptorCreate*" Twitter
otool -l Twitter |grep cry  # 查看ipa是否加密，0表示未加密
file DamnVulnerableIOSApp
lipo -thin armv7 DamnVulnerableIOSApp -output DVIA32 # 将多架构通用程序转换成单架构
frida -U -l /tmp/a.js -f com.android.chrome --no-pause
idevicesyslog # 查看控制台日志
objection -d -g 1330  explore # frida 有时通过包名找不到进程，推荐使用pid
```

#### 7. A step-by-step Android penetration testing guide for beginners
https://www.hackthebox.com/blog/intro-to-mobile-pentesting

#### 8. dynamic debugg

```bash
getprop ro.debuggable
magisk resetprop  ro.debuggable 1
````
