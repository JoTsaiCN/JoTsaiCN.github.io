---
title: 使用Appium测试iOS应用(Python)
date: 2017-08-26 14:36:55
tags: [Appium, Python, iOS]
categories: [自动化测试]
---

## 环境要求

- macOS 12
- iOS 10
- Appium 的 iOS 环境搭建完毕

<!-- more -->

## 安装 Appium 的 python 客户端库

1. 安装 python
   - 下载安装包安装 [下载地址](https://www.python.org/downloads/mac-osx/)

   - 通过 brew 安装

     ```shell
     brew install python3
     ```

2. 安装 Appium 的 python 客户端库

   - 使用 pip 安装

     ```shell
     pip3 install Appium-Python-Client
     ```

   - 通过编译源码安装

     ```shell
     git clone git@github.com:appium/python-client.git
     cd python-client
     python3 setup.py install
     ```

## 启动 Appium Server

- Appium 桌面版本：运行 Appium desktop，点击 `Start Server` 按钮

- Appium 命令行版本：在终端中执行以下命令

  ```shell
  appium
  ```

  ![image](http://ws3.sinaimg.cn/large/73f872f5gy1fhoewa57d9j20eb01dglj.jpg)

## 启动 Session

### 指定测试应用

- 有三种方式指定测试应用，此时默认上下文为 `native` 类型
  - desired_capabilities 指定 `app` 关键字为 `.ipa` 文件或 `.app` 文件的本地路径
  - desired_capabilities 指定 `app` 关键字为 `.ipa` 文件或 `.app` 文件的 `HTTP` 地址
  - desired_capabilities 指定 `bundleId` 关键字为应用的 `Bundle Identifier`，这种方式要求应用在创建 session 前已安装在设备上
- 使用以下方式指定测试 web 时要启动的浏览器，此时默认上下文为 `webview` 类型
  - desired_capabilities 指定 `browserName` 关键字为浏览器名称，指定 `startIWDP` 关键字为 True

### 重置策略

- desired_capabilities 指定 `fullReset` 关键字或 `noReset` 关键字的值为 `true` 来设置重置策略


- `fullReset ` 和 `noReset` 默认为 `false`，此时采用 `默认重置策略`

|      | 默认                      | fullReset                     | noReset                  |
| ---- | ----------------------- | ----------------------------- | ------------------------ |
| iOS  | 真机测试前卸载已安装的 app。不重启模拟器。 | 真机测试前卸载已安装的 app。模拟器测试前会重启模拟器。 | 真机测试前不卸载已安装的 app。不重启模拟器。 |

```python
# fullReset
desired_capabilities={
    '……省略……': '……省略……',
    'fullReset': True
}
# noReset
desired_capabilities={
    '……省略……': '……省略……',
    'noReset': True
}
```

### 创建和结束 Session

- 创建 session `webdriver.Remote(command_executor, desired_capabilities)`

- 结束 session `WebDriver.quit()`


```python
from appium import webdriver
driver = webdriver.Remote(
    command_executor='http://0.0.0.0:4723/wd/hub',
    desired_capabilities={
        'platformName': 'iOS',
        'platformVersion': '10.1.1',
        'deviceName': 'iPhone6',
        'udid': '设备udid',
        'app': 'app路径'            
    }
)
driver.quit()
```

- 参数

  - command_executor: 字符串类型，appium server 监听的地址
  - desired_capabilities: 字典类型，启动 session 的参数

- 说明

  - `webdriver.Remote` 返回一个 `appium.webdriver.webdriver.WebDriver` 对象
  - `WebDriver.quit` 返回 `None`
  - 如果无法创建或结束 session，会抛出 `WebDriverException` 异常


## 设备操作

### 重启 session  

WebDriver.reset()  

```python
driver.reset()
```

【说明】
-  返回 WebDriver 对象

### app 操作

  - 卸载 `WebDriver.remove_app(app_id)`

  - 安装 `WebDriver.install_app(app_path)`

  - 启动 `WebDriver.launch_app()`

    ```python
    driver.remove_app('com.GuangZhouXuanWu.iphoneEtion')
    driver.install_app('app_path')
    driver.launch_app()
    ```
    【参数】
    - app_id: app 的 bundleId
    - app_path: app 安装包文件路径

    【说明】
    - 返回 WebDriver 对象

    - launch_app 启动的是 app 关键字指定的应用，所以不需要参数


### 上下文操作

- 获取上下文 `WebDriver.contexts`  `WebDriver.context`  `WebDriver.current_context`

  ```python
  driver_contexts = driver.contexts
  driver_context = driver.context
  driver_current_context = driver.current_context
  ```

  【说明】
  - WebDriver.context 返回一个字符串，为当前上下文名称
  - WebDriver.contexts 返回一个包含当前会话所有上下文名称的 list
  - WebDriver.current_context 等价于 WebDriver.context

- 切换上下文 `WebDriver.switch_to.context(context_name)`

  ```python
  driver.switch_to.context('WEBVIEW_1')
  # 切换到 webview 类型的上下文后，使用 web 自动化 API
  driver.get('http://www.baidu.com')
  web_element = driver.find_element(MobileBy.CSS_SELECTOR, 'button.se-bn')
  driver.switch_to.context('NATIVE_APP')
  ```

  【说明】
  - 返回 `None`

### 获取 UI 布局

WebDriver.page_source

```python
driver_page_source = driver.page_source
```
【说明】
- 当前上下文为 native 时，返回一个 xml 格式的字符串
- 当前上下文为 webview 时，返回一个 html 格式的字符串

### 获取分辨率

WebDriver.get_window_size()

```python
driver_size = driver.get_window_size()
```

【说明】
- 返回一个字典
- 返回逻辑分辨率
- 关于 iPhone 的分辨率可参考该网址 [ultimate-guide-to-iphone-resolutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)

### 屏幕截图

  - 返回二进制数据 `WebDriver.get_screenshot_as_png()`

  - 返回一个字符串 `WebDriver.get_screenshot_as_base64()`

  - 保存到文件中 `WebDriver.get_screenshot_as_file(filename)`

  - 保存到文件中 `WebDriver.save_screenshot(filename)`

    ```python
    # bytes
    image_bytes = driver.get_screenshot_as_png()
    # base64
    image_base64 = driver.get_screenshot_as_base64()
    html_str = '<html><body><img src="data:image/png;base64, %s"/></body></html>' % image_base64
    html_file = open('./image.html', 'w')
    html_file.write(html_str)
    html_file.close()
    # file
    driver.get_screenshot_as_file('./save_image.png')
    ```

    【参数】

    - filename: 字符串类型，指定保存路径

    【说明】

    - 返回的截图分辨率大小为渲染分辨率
    - get_screenshot_as_png 返回 bytes 类型
    - get_screenshot_as_base64 返回字符串类型，适用于将图片嵌入到 HTML 中
    - get_screenshot_as_file 成功时返回 `True`，失败返回 `False`
    - save_screenshot 与 get_screenshot_as_file 等价

### 弹窗操作

  - 接受弹窗 `WebDriver.switch_to.alert.accept()`

  - 拒绝弹窗 `WebDriver.switch_to.alert.dismiss()`

  - 获取文本 `WebDriver.switch_to.alert.text`

    ```python
    driver.switch_to.alert.accept()
    driver.switch_to.alert.dismiss()
    alert_text = WebDriver.switch_to.alert.text
    ```

    【说明】

    - 返回 `None`

### 屏幕操作

- 点击 `WebDriver.tap(positions, duration)`

  ```python
  driver.tap([(200, 300)], 200)
  # 暂时不支持多点触碰
  driver.tap([(50, 50), (150, 150)], 200)
  ```

  【参数】
  - positions: 类型为 list，列出所有点击坐标，点击坐标格式为 `(x, y)`
  - duration: 点击的时长，单位为 ms，默认为 `None`

  【说明】
  - 返回 WebDriver 对象
  - 最多可以有 5 个点击坐标，对应最多 5 个手指的多点触碰

- 滚动 `WebDriver.scroll(origin_el, destination_el)`

  ```python
  destination_el = driver.find_element(MobileBy.ACCESSIBILITY_ID, '列表-cell-农夫山泉')
  driver.scroll(None, destination_el)
  ```

  【参数】
  - origin_el: WebElement 对象，表示滚动的起始位置
  - destination_el: WebElement 对象，表示滚动的结束位置

  【说明】
  - 返回 WebDriver 对象
  - scroll 方法为滚动界面直到 destination_el 元素显示在屏幕上，因此 origin_el 参数没有意义，传 None 即可

- 划动 `WebDriver.swipe(start_x, start_y, end_x, end_y, duration)`

  ```python
  driver.swipe(200, 400, 0, -100, 100)
  ```

  【参数】
  - start_x: 起始位置的 x 轴坐标
  - start_y: 起始位置的 y 轴坐标
  - end_x: x 轴偏移量
  - end_y: y 轴偏移量
  - duration: 点击起始位置和开始划动的间隔时间，默认为 `None`

  【说明】
  - 返回 WebDriver 对象

### 执行指令

WebDriver.execute_script(script, *args)

```python
destination_el = driver.find_element(MobileBy.ACCESSIBILITY_ID, '列表-cell-农夫山泉')
driver.execute_script('mobile: scroll', {'element': destination_el, 'toVisible': True})
```

【参数】

- script: 执行的指令
- args: 指令的参数

【说明】

- 在 native 类型的上下文中使用 `mobile: 指令` 指定要执行的操作，详细说明查看 [XCTest 手势操作]()

- 在 webview 类型的上下文中与 web 自动化的用法一样

### 后台运行

WebDriver.background_app(seconds)

```python
driver.background_app(1)
```

【参数】
- seconds: 后台运行的时长，单位为秒

【说明】
- 将 app 后台运行指定时长后，再将 app 前台运行
- 返回 WebDriver 对象

### 获取设备时间

WebDriver.device_time

```python
import datetime
device_time_str = driver.device_time
device_time = datetime.datetime.strptime(device_time_str[:-6], '%a %b %d %Y %H:%M:%S %Z%z')
device_time = device_time - datetime.timedelta(hour=14)
```

【说明】
- 返回字符串
- 通过执行 idevicedate 命令来获取设备时间
- 这里存在一个问题，idevicedate 返回的时间为 `Fri Aug  4 15:29:15 CST 2017`，CST 可以代表 4 个时区，在这里表示 `中国标准时间`，但 appium 将 CST 当成 `美国中部标准时间`，因此返回的时间比实际快 14 个小时

### 获取本地化字符串

WebDriver.app_strings(language, string_file)

```python
device_app_strings = driver.app_strings(language='zh-Hans')
```

【参数】
- language: 指定要获取哪一种语言的本地化文件
- string_file: 指定本地化文件的文件名

【说明】
- 返回一个字典
- 需要在 desired_capabilities 中指定 app 关键字
- 实际上是通过解压本地的 app 包来获取 [language].lproj 文件夹（比如 zh-Hans.lproj）下的 [string_file] 文件（默认为 Localizable.strings），所以调用该方法的时候需要注意 iOS 上安装的包是否与 app 关键字指定的安装包一致

## 查找元素

### 查找单个元素

 WebDriver.find_element(by, value) 

```python
from appium.webdriver.common.mobileby import MobileBy 
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
```
### 查找多个元素

 WebDriver.find_elements(by, value)

```python
from appium.webdriver.common.mobileby import MobileBy 
elements = driver.find_elements(MobileBy.ACCESSIBILITY_ID, '登录')
```
### 参数

- by: 定位策略
- value: 定位策略的参数

### 说明

- `find_element` 返回一个 `WebElement` 对象，对应找到的第一个元素，`find_elements` 返回一个 `list` ，包含找到的所有元素的
- 如果找不到元素，`find_element` 会抛出 `NoSuchElementException` 异常，`find_elements` 会返回一个空列表


- WebDriver.find_element(By.XXX, value) 等价于 WebDriver.find_element_by_xxx(value)

  ```python
  element = driver.find_element_by_accessibility_id('登录')
  ```

- WebDriver.find_elements(By.XXX, value) 等价于 WebDriver.find_elements_by_xxx(value)
  ```python
  elements = driver.find_elements_by_accessibility_id('登录')
  ```

### 定位策略

Appium 支持**部分 **WebDriver 定位策略

#### class name 

按 UI 元素类型查找

  ```python
element = driver.find_element(MobileBy.CLASS_NAME, 'XCUIElementTypeButton')
  ```

#### xpath

  ```python
element = driver.find_element(MobileBy.XPATH, '//XCUIElementTypeApplication[@name="玄讯快销100"]/XCUIElementTypeWindow[1]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeTable/XCUIElementTypeCell[4]/XCUIElementTypeButton[1]')
  ```

#### id

在 iOS 设备上与 `accessibility id` 等价

  ```python
element = driver.find_element(MobileBy.ID, '登录')
  ```

#### name

在 iOS 设备上与 `accessibility id` 等价

  ```python
element = driver.find_element(MobileBy.NAME, '登录')
  ```

Appium还添加了对 [Mobile JSON 连接协议](https://github.com/SeleniumHQ/mobile-spec/blob/master/spec-draft.md) **部分**定位策略的支持
#### accessibility id

  ```python
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
  ```

#### ios predicate

限 iOS，通过使用多个属性匹配条件来定位元素，常见属性有 type, value, name, label, enabled, visible 

##### 比较运算符

==, >=, <= , \>, <, !=, BETWEEN {n, m}, IN {s1, s2, s3}

```python
element = driver.find_element(MobileBy.IOS_PREDICATE, 'type == "XCUIElementTypeButton"')
element = driver.find_element(MobileBy.IOS_PREDICATE, 'value BETWEEN {10, 20}')
element = driver.find_element(MobileBy.IOS_PREDICATE, 'name IN {"登录", "用户名", "密码"}')
```

##### 逻辑运算

AND, OR, NOT 

```python
element = driver.find_element(MobileBy.IOS_PREDICATE, 'visible == true AND label == "登录"')
```

##### 字符串比较

BEGINSWITH, CONTAINS, ENDSWITH, LIKE, MATCHES （MATCHES 根据 [ICUv3 的正则表达式](http://userguide.icu-project.org/strings/regexp)进行匹配）

```python
element = driver.find_element(MobileBy.IOS_PREDICATE, 'name CONTAINS "Login"')
```

#### ios class chain

限 iOS 9.3 及以上

  ```python
element = driver.find_element('-ios class chain', 'XCUIElementTypeWindow[1]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeTable/XCUIElementTypeCell[4]/XCUIElementTypeButton[1]')
  ```

#### ios uiautomation

限 iOS 9.3 及以下

#### android uiautomator

限 Android


#### 如何选择定位策略

  - 尽量使用 `accessibility id` 和 `ios predicate` 
  - 其次使用 `ios class chain` 和 `ios uiautomation` ，因为这两个定位策略有操作系统版本限制
  - 尽量不要使用 `xpath`，尽管 xpath 不受操作系统版本限制，但是因为 iOS 并没有原生支持 xpath，是由 Appium 服务器解析整个 UI 布局来查找的，速度非常慢，比如上面的 xpath 示例耗时 4s 多，而类似的 ios class chain 示例耗时为 1.5s

#### 如何确定设备支持的定位策略

  每次向 appium 服务器发送查找元素的请求时，服务器会有如下的日志输出，列出设备支持的所有定位策略

  ```
[BaseDriver] Valid locator strategies for this request: xpath, id, name, class name, -ios predicate string, -ios class chain, accessibility id
  ```

### 等待策略

Appium 支持 WebDriver 的 2 种等待策略，在指定的等待时间中不断查找元素，直到 `找到元素` 或者 `超时`

#### 隐式等待

WebDriver.implicitly_wait(time_to_wait)

```python
driver.implicitly_wait(10)
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
driver.implicitly_wait(0)
```

【参数】

- time_to_wait: 为 0 时表示关闭隐式等待，大于 0 时表示开启隐式等待且等待时间设为 `time_to_wait`，单位为秒；创建session 时默认为关闭隐式等待

【说明】
- 隐式等待一旦开启，在关闭隐式等待或者结束 session 之前，调用元素查找方法的等待策略都为隐式等待
- 如果在 time_to_wait 时间内找不到元素，抛出 `NoSuchElementException` 异常

```python
driver.implicitly_wait(10)
# 找不到元素
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '找不到的元素')
# 抛出的异常
"""
……省略……
selenium.common.exceptions.NoSuchElementException: Message: An element could not be located on the page using the given search parameters.
"""
```
#### 显式等待

- 反复调用 method 方法，直到该方法返回值为 `True`

  WebDriverWait(driver, timeout, poll_frequency, ignored_exceptions).until(method, message)

  ```python
  from selenium.webdriver.support.wait import WebDriverWait
  # 返回找到的元素
  element = WebDriverWait(driver, 5, 1).until(lambda x: x.find_element(MobileBy.ACCESSIBILITY_ID, '登录'))
  ```

- 反复调用 method 方法，直到该方法返回值为 `False`

  WebDriverWait(driver, timeout, poll_frequency, ignored_exceptions).until_not(method, message)

  ```python
  from selenium.webdriver.support.wait import WebDriverWait
  # 返回 True
  not_found = WebDriverWait(driver, 5, 1).until_not(lambda x: x.find_element(MobileBy.ACCESSIBILITY_ID, '找不到的元素'))
  ```
  【参数】

  - driver: WebDriver 对象，作为 method 方法的第一个参数
  - timeout: 超时时间
  - poll_frequency: 执行间隔，默认为 0.5 秒
  - ignored_exceptions: 执行方法时忽略的异常，可以有多个，默认为 NoSuchElementException
  - method: 执行的方法
  - message: 抛出 `TimeoutException` 时附带的信息，默认为空字符串

  【说明】

  - 在 timeout 时间内，每隔 poll_frequency 秒调用一次 method 方法，直到超时或者 method 方法的返回值为 `True / False`
  - 返回值为 method 方法的返回值
  - 在调用 method 方法的时候，如果抛出的异常在 ignored_exceptions 里则忽略并继续，否则抛出异常
  - 超时会抛出 `TimeoutException` 异常，无法用 ignored_exceptions 忽略

  ```python
  from selenium.webdriver.support.wait import WebDriverWait
  # 找不到元素
  element = WebDriverWait(driver, 5).until(lambda x: x.find_element(MobileBy.ACCESSIBILITY_ID, '找不到的元素'))
  # 抛出的异常
  """
  ……省略……
  selenium.common.exceptions.TimeoutException: Message: 
  """
  ```


## 元素操作

### 获取属性

-  获取指定属性 `WebElement.get_attribute(name)`

   ```python
   element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
   element_label = element.get_attribute('label')
   ```

   【参数】

   -  name: 属性名称，字符串

   【说明】

   -  返回指定属性的值，通常为字符串类型。常用的属性有 name、value、type、label
   -  属性不存在时抛出 `WebDriverException` 异常


- 获取元素的坐标和大小 `WebElement.location`  `WebElement.size`  `WebElement.rect`

  ```python
  element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
  element_location = element.location # {'x': 15, 'y': 190}
  element_size = element.size         # {'width': 17, 'height': 17}
  element_rect = element.rect         # {'width': 17, 'height': 17, 'x': 15, 'y': 190}
  ```

  【说明】

  - 返回值为字典类型

- 获取元素的类型和文本值 `WebElement.tag_name` 和 `WebElement.text`

  ```python
  element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
  element_tag = element.tag_name # XCUIElementTypeButton
  element_text = element.text    # 登录
  ```

  【说明】

  - 返回值为文本


- 获取显示状态 `WebElement.is_displayed()`

  ```python
  element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
  element_is_displayed = element.is_displayed()
  ```

  【说明】

  - 返回值为 bool 类型

### 点击

 WebElement.click()

```python
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, '登录')
element.click()
```

【说明】
- 返回 `None`

### 赋值

 WebElement.send_keys(value)

```python
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'LoginUserName')
element.send_keys('758002')
```

【参数】
- value: 要赋给元素的值

【说明】
- 支持 `TextField` 和 `PickerWheel` 类型的 UI 元素
- 赋值成功返回 `None`
- TextField 元素调用 send_keys 方法时：
  - 在原有的值后面添加新的值
  - 要求点击元素时能呼出键盘，否则会抛出 `WebDriverException` 异常
- issue: 如果 WebDriverAgentRunner 模拟键入的速度太快可能导致 session 出现异常，解决方法是设置 desired_capabilities， 将默认为 60 的 `maxTypingFrequency` 改小为 10 至 20 

### 清空

 WebElement.clear()

```python
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'LoginUserName')
element.send_keys('123456')
element.clear()
```

【说明】
- 支持 `TextView` 类型
- 要求点击元素时能呼出键盘，否则会抛出 `WebDriverException` 异常

### 查找元素

 WebElement.find_element(by, value)

 WebElement.find_elements(by, value)

```python
element = driver.find_element(MobileBy.ACCESSIBILITY_ID, 'TableList')
sub_element = element.find_element(MobileBy.ACCESSIBILITY_ID, '表单-今日拜访')
```

【说明】
- 参数和返回值与 WebDriver 同名 API 相同
- 以 UI 元素为根节点进行查找


## XCTest 手势操作

XCTest 提供了非常丰富的手势操作，这些操作都是 iOS 平台独有的。从 1.6.4-beta 版本开始的 Appium 支持 XCTest 手势操作。需要特别注意的是目前 XCTest 和 WDA 正在不断优化改变的阶段，这意味着所有 `mobile: *` 的命令可能会在没任何通知的情况下就被调整更改。

### mobile: swipe

这个手势是在指定的屏幕上的控件或App的控件上执行“滑动”操作，一般是针对整个屏幕。这个方法不支持通过坐标来操作，并且仅仅是简单的模拟单个手指滑动。这个方法对于诸如相册分页、切换视图等情况可能会发挥比较大的作用。更复杂的场景可能需要用到`mobile:dragFromToForDuration`，这个方法支持传坐标（coordinates ）和滑动持续时间（duration）。

【支持参数】

- *direction*: 'up', 'down', 'left' or 'right'. 这4个参数是固定的。
- *element*: 需要滑动的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。

【用法示例】 

```python
driver.execute_script('mobile: swipe', {'direction': 'down', 'element': element});
```

### mobile: scroll

滚动元素或整个屏幕。支持不同的滚动策略。该方法提供了4个可选择滑动策略：按照顺序有“name”，“direction”，“predicateString”或“toVisible”。所有的滑动策略都是排他性的，一次滑动只能选择一个策略。你可以使用`mobile:scroll`来对表格中或者集合视图中的某个已知控件进行精确的滚动操作。然而目前有一个已知的局限问题：如果需要在父容器上执行太多的滚动手势来达到必要的子元素（其中几十个），则方法调用可能会失败。

【支持参数】

- *element*: 需要滚动的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。
- *name*: 需要执行滚动的子控件的`accessibility id`。 将`predicateString`参数设置为`“name == accessibilityId”`可以实现相同的结果。如果`element`不是容器，则不起作用。
- *direction*: 'up', 'down', 'left' or 'right'. 该参数与`swipe`中的比，差别在于`scroll`会尝试将当前界面完全移动到下一页。（`page`一词表示单个设备屏幕中的所有内容）
- *predicateString*: 需要被执行滚动操作的子控件的NSPredicate定位器。如果控件不是容器，则不起作用。
- *toVisible*: 布尔类型的参数。如果设置为`true`，则表示要求滚动到父控件中的第一个可见到的子控件。如果`element`未设置，则不生效。

【用法示例】

```python
driver.execute_script('mobile: scroll', {'direction': 'down'});
```

### mobile: pinch

在给定的控件或应用程序控件上执行捏合手势。

【支持参数】

- *element*: 需要捏合的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。
- *scale*: 浮动型夹点尺度。使用0和1之间的比例来“捏紧”或缩小，大于1的比例“撑开”或放大。强制参数
- *velocity*: 每秒缩放速度（浮点值）。强制参数

【用法示例】

```python
driver.execute_script('mobile: pinch', {'scale': 0.5, 'velocity': 1.1, 'element': element})
```

### mobile: doubleTap

在指定控件上或屏幕上执行双击手势。

【支持参数】

- *element*: 需要双击的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。
- *x*: 屏幕x轴坐标点，浮点型. 仅当`element`未设置时才是强制参数
- *y*: 屏幕y轴坐标点，浮点型. 仅当`element`未设置时才是强制参数

【用法示例】

```python
driver.execute_script('mobile: doubleTap', {'element': element});
```

### mobile: touchAndHold

在指定控件上或屏幕上长按的手势操作。

【支持参数】

- *element*: 需要长按的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。
- *duration*: 长按的持续时间（秒），浮点型。强制性参数
- *x*: 屏幕x轴坐标点，浮点型. 仅当`element`未设置时才是强制参数
- *y*: 屏幕y轴坐标点，浮点型. 仅当`element`未设置时才是强制参数

【用法示例】

```python
driver.execute_script('mobile: touchAndHold', {'duration': 2.0, 'element': element});
```

### mobile: twoFingerTap

在给定元素或应用程序元素上执行两个手指点击手势。

【支持参数】

- *element*: 需要两只手指操作的控件ID（作为十六进制哈希字符串）。如果没有提供该参数的话，则会使用App的控件作为替代。

【用法示例】

```python
driver.execute_script('mobile: twoFingerTap', {'element': element});
```

### mobile: tap

在指定控件或屏幕上的坐标执行点击手势。

【支持参数】

- *element*: 控件ID（作为十六进制哈希字符串）。 如果设置 了`element`参数，则`x`、`y`代表的是以当前`element`为边界的xy轴。若未设置，则`x`,`y`代表的是以手机屏幕为边界。
- *x*: x轴坐标，类型为float。强制参数
- *y*: y轴坐标，类型为float。强制参数

【用法示例】

```python
driver.execute_script('mobile: tap', {'x': 100, 'y': 50, 'element': element});
```

### mobile: dragFromToForDuration

通过坐标点执行拖放手势。可以在控件上执行，也可以在屏幕上执行。

【支持参数】

- *element*: 控件ID（作为十六进制哈希字符串）。 如果设置 了`element`参数，则`x`、`y`代表的是以当前`element`为边界的xy轴。若未设置，则`x`,`y`代表的是以手机屏幕为边界。
- *duration*: 浮点数范围[0.5,60]。表示开始拖动点之前的点击手势需要多长时间才能开始拖动。强制参数
- *fromX*: 起始拖动点的x坐标（类型float）。强制参数
- *fromY*: 起始拖动点的y坐标（类型float）。强制参数
- *toX*: 结束拖曳点的x坐标（float类型）。强制参数
- *toY*: 结束拖动点的y坐标（类型float）。强制参数

【用法示例】

```python
driver.execute_script('mobile: dragFromToForDuration', {'duration': 1, 'fromX': 100, 'fromY': 100, 'toX': 200, 'toY': 200, 'element', element});
```

### mobile: selectPickerWheelValue

选择下一个或上一个picker wheel的值。 如果这些值是动态的，那么这个方法是能起作用的。XCTest有一个BUG就是你并不能知道要选择哪一个或者当前的选择区域是否生效。

【支持参数】

- *element*: PickerWheel的内部元素id（作为十六进制哈希字符串）执行值选择。元素必须是XCUIElementTypePickerWheel类型。强制参数
- *order*: `next` 选择下一个value，`previous`选择前面一个value。强制参数
- *offset*: 区间值： [0.01, 0.5]。它定义了picker wheel的中心距离应该有多远。 通过将该值乘以实际的picker wheel高度来确定实际距离。太小的偏移值可能不会改变picker wheel的值，而过高的值可能会导致picker wheel同时切换两个或多个值。通常最优值位于范围[0.15,0.3]中。默认为0.2

【用法示例】

```python
driver.execute_script('mobile: selectPickerWheelValue', {'order': 'next', 'offset': 0.15, 'element': element});
```

### mobile: alert

对弹窗执行操作。

【支持参数】

- *action*: 支持以下操作: `accept`, `dismiss` and `getButtons`。强制参数
- *buttonLabel*: 点击已有警报按钮的标签文本。这是一个可选参数，只能与`accept`和`dismiss` 操作相结合才有效。

【用法示例】

```python
driver.execute_script('mobile: alert', {'action': 'accept', 'buttonLabel': 'My Cool Alert Button'});
```
