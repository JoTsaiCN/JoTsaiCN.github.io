---
title: Appium简介
date: 2017-08-26 13:55:15
tags: [Appium, iOS]
categories: [自动化测试]
---

## Appium 简介

> Appium is an open-source tool for automating native, mobile web, and hybrid applications on iOS mobile, Android mobile, and Windows desktop platforms. **Native apps** are those written using the iOS, Android, or Windows SDKs. **Mobile web apps**are web apps accessed using a mobile browser (Appium supports Safari on iOS and Chrome or the built-in 'Browser' app on Android). **Hybrid apps** have a wrapper around a "webview" -- a native control that enables interaction with web content. Projects like [Phonegap](http://phonegap.com/), make it easy to build apps using web technologies that are then bundled into a native wrapper, creating a hybrid app.
>
> Importantly, Appium is "cross-platform": it allows you to write tests against multiple platforms (iOS, Android, Windows), using the same API. This enables code reuse between iOS, Android, and Windows testsuites.

- 开源的自动化测试工具
- 支持原生、移动 web 和混合应用
- 跨平台，对 iOS、Android、Windows 进行自动化测试时使用相同的 API

<!-- more -->

## Appium 的理念

> Appium was designed to meet mobile automation needs according to a philosophy outlined by the following four tenets:
>
> 1. You shouldn't have to recompile your app or modify it in any way in order to automate it.
> 2. You shouldn't be locked into a specific language or framework to write and run your tests.
> 3. A mobile automation framework shouldn't reinvent the wheel when it comes to automation APIs.
> 4. A mobile automation framework should be open source, in spirit and practice as well as in name!

1. 不需要为了自动化而重新编译或者修改应用
2. 不局限于某种语言或者框架来编写和运行测试脚本
3. 一个移动端自动化框架不应该在自动化接口上重复造轮子（移动自动化的接口应该统一）
4. 必须开源

## Appium 的设计

为了实现上述4条理念，appium 设计如下

1. 在 appium 引擎下使用操作系统提供的自动化框架
    - iOS 9.3 及以上: 苹果的 [XCUITest](https://developer.apple.com/reference/xctest)
    - iOS 9.3 及以下: 苹果的 [UIAutomation](https://developer.apple.com/library/ios/documentation/DeveloperTools/Reference/UIAutomationRef/)
    - Android 4.2+: 谷歌的 [UiAutomator](http://developer.android.com/tools/help/uiautomator/index.html)
    - Android 2.3+: 谷歌的 [Instrumentation](http://developer.android.com/reference/android/app/Instrumentation.html) （[Selendroid](http://selendroid.io/) 提供 Instrumentation 的支持）
    - Windows: 微软的 [WinAppDriver](http://github.com/microsoft/winappdriver)

2. 将上述自动化框架封装成一套 API：WebDriver API， WebDriver 指定了客户端-服务端协议（JSON Wire Protocol）。基于客户端-服务端的架构，我们可以使用任何编程语言来编写客户端，向服务端发送恰当的 HTTP 请求，以 iOS 为例，appium 的架构如下图所示。目前 appium 已经实现了大多数流行语言版本的客户端，这意味着我们可以使用任何测试套件或者测试框架

   ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhny04nxunj20ll0ip0t7.jpg)

3. 扩充 WebDriver 的协议，实现了 Mobile JSON Wire Protocol，在 JSON Wire Protocol 的基础上添加移动自动化相关的 API 方法

4. [Appium 的 github](https://github.com/appium)

## Appium 的概念

**C/S 架构**
Appium 的核心是一个 web 服务器，它提供了一套 REST 的接口。它收到客户端的连接，监听到命令，接着在移动设备上执行这些命令，然后将执行结果放在 HTTP 响应中返还给客户端。事实上，这种客户端/服务端的架构给予了许多的可能性：比如我们可以使用任何实现了该客户端的语言来写我们的测试代码。比如我们可以把服务端放在不同的机器上。比如我们可以只写测试代码，然后使用像 [Sauce Labs](https://saucelabs.com/mobile) 这样的云服务来解释命令。

**Appium 服务端**
Appium server 是用 Node.js 写的。我们可以用源码编译或者从 NPM 直接安装。

**Appium 客户端**
Appium 客户端有很多语言库 Java, Ruby, Python, PHP, JavaScript 和 C#，这些库都实现了 Appium 对 WebDriver 协议的扩展。当使用 Appium 的时候，只需使用这些库代替常规的 WebDriver 库就可以了。 以下是目前的客户端类库列表以及对应的项目开源地址，可以访问 [网址](https://github.com/appium/appium/blob/master/docs/en/about-appium/appium-clients.md)查看所有的库的列表。

| 语言/框架             | Github版本库以及安装指南                          |
| -------------------- | ---------------------------------------- |
| Ruby                 | [https://github.com/appium/ruby_lib](https://github.com/appium/ruby_lib) |
| Python               | [https://github.com/appium/python-client](https://github.com/appium/python-client) |
| Java                 | [https://github.com/appium/java-client](https://github.com/appium/java-client) |
| JavaScript (Node.js) | [https://github.com/admc/wd](https://github.com/admc/wd) |
| Objective C          | [https://github.com/appium/selenium-objective-c](https://github.com/appium/selenium-objective-c) |
| PHP                  | [https://github.com/appium/php-client](https://github.com/appium/php-client) |
| C# (.NET)            | [https://github.com/appium/appium-dotnet-driver](https://github.com/appium/appium-dotnet-driver) |
| RobotFramework       | [https://github.com/jollychang/robotframework-appiumlibrary](https://github.com/jollychang/robotframework-appiumlibrary) |

**Session（会话）**
自动化始终围绕一个 session 进行，客户端初始化一个 seesion（会话）来与服务端交互，发送一个 POST 请求给服务端，请求中包含一个 JSON 对象，被称作“desired capabilities”，服务端会根据 desired capabilities 开启一个自动化的 session，然后返回一个 session ID，客户端使用这个 session ID 发送后续的命令。

**Desired Capabilities：创建Session 所需的参数**
Desired capabilities 是一些键值对 (key-value) 的集合，客户端将这些键值对发给服务端，告诉服务端我们想要怎么测试。比如如下所示的 Desired capabilities，表示测试的设备是系统为 `iOS`、系统版本为 `10.3` 、设备名为 `iPhone SE` 的模拟器，测试 app 的 bundleId 为 `com.apple.mobilesafari`，也就是系统自带的 safari 浏览器。访问 [capabilities文档](https://github.com/appium/appium/blob/master/docs/en/writing-running-appium/caps.md) 查看完整的 capabilities 列表。

```json
{
  "platformName": "iOS",
  "platformVersion": "10.3",
  "deviceName": "iPhone SE",
  "bundleId": "com.apple.mobilesafari"
}
```

**Appium 服务端的桌面版：appium-desktop.dmg, appium-desktop.exe**
Appium 提供了 GUI 封装的 Appium 服务端下载，它封装了运行 Appium 服务端的所有依赖，而不需要担心怎样安装 Node.js。其中还包括一个 Inspector 工具，可以帮助你检查应用的界面层级，这样写测试用例时更方便。

## WebDriverAgent 介绍

> WebDriverAgent is a [WebDriver server](https://w3c.github.io/webdriver/webdriver-spec.html) implementation for iOS that can be used to remote control iOS devices. It allows you to launch & kill applications, tap & scroll views or confirm view presence on a screen. This makes it a perfect tool for application end-to-end testing or general purpose device automation. It works by linking `XCTest.framework` and calling Apple's API to execute commands directly on a device. WebDriverAgent is developed and used at Facebook for end-to-end testing and is successfully adopted by [Appium](http://appium.io/).

WebDriverAgent 是 Facebook 的一个开源项目，它在 iOS 端实现了一个 WebDriver server，借助这个 server 我们可以远程控制 iOS 设备，可以进行启动和终止 app、点击、滚动屏幕、确认显示内容是否正确等操作。WebDriverAgent 通过链接 `XCTest.framework` 调用 Apple 的 API 直接在设备上执行命令。
