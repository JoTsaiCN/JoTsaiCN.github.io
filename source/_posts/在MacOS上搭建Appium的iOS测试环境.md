---
title: 在MacOS上搭建Appium的iOS测试环境
date: 2017-08-26 14:21:07
tags: [macOS, Appium, iOS]
categories: [自动化测试]
---

*本文档基于Xcode8.3.3，appium-desktop-v1.1.1，appium-v1.6.5*



## 准备环境和文件

- Mac OS 10.12
- iOS 10
- Apple ID，没有的可以访问[创建您的Apple ID](https://appleid.apple.com/account#!&page=create)注册
- Xcode 8，可从[Apple开发者中心](https://developer.apple.com/download/more/)下载 xip 安装文件，或者直接从 App Store 下载安装
- Node.js，选择 LTS 的 Macintosh Installer 版本 [下载页面](https://nodejs.org/en/download/)
- appium desktop，Appium 的桌面版，选择 Latest release 的 dmg 文件 [下载页面](https://github.com/appium/appium-desktop/releases)

<!-- more -->

## 安装 Appium

* *P.S. 以下安装如果**报错**没有权限时，以修改目录权限的方式解决，不要使用 sudo 或切换到 root 进行安装，以免后续每次运行都需要修改相关权限*

  ```shell
  sudo chmod -R 777 路径
  ```

1. 双击 Xcode 安装文件解压，将解压后的文件拉到应用程序中，打开 Xcode 同意 lisence

2. 安装 Xcode Command Line Tools

   ```shell
   xcode-select --install
   ```


3. 安装 homebrew，这是用于安装后续工具的包管理器

   ```shell
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   ```


4. 安装 libimobiledevice，这是用于连接 iOS 设备的开源工具，类似于 Android 的 ADB

   ```shell
   brew install libimobiledevice --HEAD
   ```


5. 安装 carthage，WebDriverAgent 使用的依赖管理工具

   ```shell
   brew install carthage
   ```


6. 双击打开 Node.js 的 pkg 文件，根据提示安装即可，用于安装后续工具

7. 使用淘宝 npm 源安装 cnpm，因为 npm 安装的时候很慢而且经常失败，后面使用 cnpm 代替 npm

   ```shell
   npm install -g cnpm --registry=https://registry.npm.taobao.org
   ```

8. 安装 ios-deploy，这是支持使用命令行管理 iOS 设备 app 的工具

   ```shell
   cnpm install -g ios-deploy
   ```

9. 双击打开 appium desktop 的 dmg 文件，在打开的窗口中将左边的 Appium 图标拖至右边的 Applications (应用程序) 图标即可；如果想要安装命令行版本，则执行以下命令安装，两个版本可以共存

   ```shell
   cnpm install -g appium
   ```

## 验证安装

1. 安装 appium doctor

   ```shell
   cnpm install -g appium-doctor
   ```


2. 运行 appium doctor，检查 appium 的 iOS 环境是否安装完毕

   ```shell
   appium-doctor --ios
   ```
   ![image](http://ws1.sinaimg.cn/large/73f872f5gy1fhjkx6yhnpj20h005xjs1.jpg)

## 常用命令

1. idevice_id 获取已连接的 iOS 设备的 udid

   ```shell
   idevice_id -l
   ```

2. idevicename 获取已连接的 iOS 设备的设备名称

   ```shell
   idevicename
   idevicename -u [udid]
   ```

3. ideviceinfo 获取 iOS 设备信息

   ```shell
   ideviceinfo
   ideviceinfo -u [udid]
   ideviceinfo -u [udid] -k [key name]
   ```

4. ios-deploy 管理 iOS 设备的 app

   ```shell
   ios-deploy -B                  # 获取iOS设备上所有app的bundleID
   ios-deploy -b [ipa文件路径]     # 将指定的ipa安装到iOS设备上
   ```


## 在 iOS 设备上安装 WebDriverAgent

1. 安装 WebDriverAgent 依赖，命令行版本可能会提示 `Error: Cannot find module 'eslint-config-appium'`，不会影响正常功能

   ```shell
   # Appium 桌面版本
   cd  /Applications/Appium.app/Contents/Resources/app/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent
   sh ./Scripts/bootstrap.sh

   # Appium 命令行版本
   cd /usr/local/lib/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent
   sh ./Scripts/bootstrap.sh
   ```


2. 使用 Xcode 打开 WebDriverAgent 项目：点击 `Finder→前往→前往文件夹…`，输入以下路径，点击前往，双击文件夹下的 `WebDriverAgent.xcodeproj` 打开项目

   ```shell
   # Appium 桌面版本
   /Applications/Appium.app/Contents/Resources/app/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent

   # Appium 命令行版本
   /usr/local/lib/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent
   ```


3. 在 Xcode 中添加开发者账号：点击上方菜单栏的 `Xcode→Preferences→Accounts`，点击左下角的 `＋` 按钮，选择 `add Apple ID`，添加自己的 Apple ID 作为开发者账号

  ```
  *免费开发者账号的Provisioning Profile只有7天有效期，7天之后需要使用Xcode将app重新安装一次
  ```

4. 编译 WebDriverAgentLib：

  * 勾选 `Automatically manage signing`，设置 Signning 的 `Team` 为上一步添加的个人开发者账号，点击右上角的编译按钮，编译成功上方会提示 Build WebDriverAgentLib: Succeeded

    ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3eg7vnbj20zs0datbj.jpg)

5. 编译 WebDriverAgentRunner

   - 修改 `Bundle Identifier` 为 `com.XXXXXX.WebDriverAgentRunner`，必须是没有人用过的

     ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3exynjij20zq0eg415.jpg)

   - 勾选`Automatically manage signing`，设置 Signning 的`Team`为上一步添加的个人开发者账号，点击右上角的编译按钮，编译成功上方会提示 Build WebDriverAgentRunner: Succeeded

     ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3fe3nl3j20z90bmgo6.jpg)

   - **Bundle Identifier 是 iOS 应用的唯一标识，如果 Bundle Identifier 已有人使用，会导致无法生成 app 的 Provisioning Profile**，如下所示

     ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3ocs04yj20rx0dz76c.jpg)

6. 将 WebDriverAgentRunner 安装到 iOS 设备

   *以下步骤在使用 Appium 连接 iOS 设备时会自动执行*

   - 有两种方式将 WebDriverAgentRunner 安装到 iOS 设备

     - 在 Xcode 中，将 Target 切换为 WebDriverAgentRunner，目标设备选择已连接的 iOS 设备，点击菜单栏的 `Product→Test`

       ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fhp1f4qy0zj20hc09dq5k.jpg)

     - 执行以下命令将 WebDriverAgentRunner 安装到 iOS 设备

       ```shell
       # Appium 桌面版本
       cd  /Applications/Appium.app/Contents/Resources/app/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent
       xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination 'id=iOS设备的udid' test

       # Appium 命令行版本
       cd /usr/local/lib/node_modules/appium/node_modules/appium-xcuitest-driver/WebDriverAgent
       xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination 'id=iOS设备udid' test
       ```

   - 上一步操作后会提示 Test Failed，可以看到 iOS 设备上多了一个叫 WebDriverAgentRunner 的 app，在 iOS 设备上点击 WebDriverAgentRunner 图标启动时会有弹窗提示 `不受信任的开发者`

     ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhlokg1tcej20up01taa0.jpg)

   - 在 iOS 设备上，进入 `设置→通用→设备管理` ，选择自己的 Apple ID 账号，点击信任 Apple ID

   - 重复第 1 步的操作，可以看到 WebDriverAgentRunner 自动启动，此时提示如下

     ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhlr71zhv5j20fo02yt8p.jpg)

   - 执行以下命令，将 iOS 设备上 WebDriverAgentRunner 监听的 8100 端口映射到 macOS 本地的 8100 端口

     ```shell
     iproxy 8100 8100 iOS设备udid
     ```

7. 调用 WebDriverAgent 接口

   - 打开浏览器，访问[http://localhost:8100](http://localhost:8100)，可以看到 json 格式的返回信息

     ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3sbijlej20tv048mxr.jpg)

   - 访问[http://localhost:8100/status](http://localhost:8100/status)，可以查看 WebDriverAgentRunner 的状态

     ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3td7n8vj20m009s0to.jpg)

   - 或者在终端中输入以下指令调用 WebDriverAgent 的接口获取对应信息

     ```shell
     curl -X POST $JSON_HEADER -d "{\"using\":\"link text\",\"value\":\"name=测试环境\"}" http://localhost:8100/session/7CD2D40C-8FCB-4306-B5C2-AD68463573BF/elements
     ```

     ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhltcx8shbj20zq04v74p.jpg)

   - 更多 url 查询可见[WebDriverAgent的github wiki](https://github.com/facebook/WebDriverAgent/wiki/Queries)，Appium 对 WebDriverAgent 的接口进行了封装，所以后续不会直接使用到这些接口


## 使用 appium desktop 连接 iOS 

### 模拟器

1. 执行以下命令查看可用的 iOS 模拟器

   ```shell
   xcrun simctl list devices
   ```

   ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia3w2puodj20p40jzn1o.jpg)

2. 运行 Appium，点击 `Start Server` 打开 appium server 控制台输出窗口，点击 `Start New Session` 打开创建新会话窗口，设置 `Desired Capabilities`；下图的 Desired Capabilities 意思是：测试的设备是系统为 `iOS`、系统版本为 `10.3`、设备名为 `iPhone SE` 的模拟器，测试的 app 为系统自带的 Safari 浏览器

   ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhlyb1imy7j20mq0dz3zy.jpg)

3. 点击右下角的 `Start Session`，此时 appium 会启动模拟器，在模拟器上启动 Safari 浏览器，并弹出 appium inspector 窗口

   ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhlynrwoptj21hc0qk42o.jpg)


### 设备

1. 运行 Appium，点击 `Start Server` 打开 appium server 控制台输出窗口，点击 `Start New Session` 打开创建新会话窗口，设置 `Desired Capabilities`；下图的 Desired Capabilities 意思是：测试的设备是系统为 `iOS`、系统版本为 `10.1.1`、设备名为 `iPhone6`、udid 为 `966498ff426206145c93……` 的设备，测试的 app 为系统自带的 Safari 浏览器

   ![image](http://ws2.sinaimg.cn/large/73f872f5gy1fia40ptxu1j20qm0fudhi.jpg)

2. 点击右下角的 `Start Session`，此时 appium 会启动 iOS 设备上的 Safari 浏览器，并弹出 appium inspector 窗口


## iOS 设备的 web 测试环境

1. 安装 ios-webkit-debug-proxy

   ```shell
   brew install ios-webkit-debug-proxy
   ```

2. 在 iOS 设备上，进入 `设置→Safari→高级`，打开 `web 检查器`

3. 在 iOS 设备上启动 Safari，访问任意网址

4. 执行以下命令，在指定的设备上启动 IWDP

   ```shell
   ios_webkit_debug_proxy -c 设备udid:端口
   ```

5. 用浏览器访问 `ip地址:端口` ，可以看到 iOS 设备上的 Safari 打开的页面，说明可以在该 iOS 设备上进行 web 自动化测试

   - ip地址：macOS 的 ip 地址，如果浏览器在 macOS 上则用 localhost 访问即可
   - 端口：第 4 步执行命令时指定的端口

   ![image](http://ws3.sinaimg.cn/large/73f872f5gy1fi9ca22svtj20qk04jjs5.jpg)

6. 在 iOS 设备上进行 web 自动化的时候，可以使用桌面浏览器的调试工具提高效率，有如下两种方式

   - 在 macOS 上打开 Safari 浏览器，点击上方菜单栏的 `开发→[设备名称]→[访问的网址]`，打开 Safari 的调试工具

        ![image](http://ws3.sinaimg.cn/large/73f872f5gy1fi9c9lka1sj20wq06k41m.jpg)

   - 使用 Chrome 浏览器访问以下网址来调试对应的 Safari 标签页

     ```
     chrome-devtools://devtools/bundled/inspector.html?ws=[ip地址]:[端口]/devtools/page/[页面序号]
     ```

     - ip地址：macOS 的 ip 地址
     - 端口：第 4 步执行命令时指定的端口
     - 页面序号：第 5 步列出的页面前面的序号

     ![image](http://ws3.sinaimg.cn/large/73f872f5gy1fi9chqxvmyj20xa09tgmw.jpg)
