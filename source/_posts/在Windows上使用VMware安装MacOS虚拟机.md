---
title: 在Windows上使用VMware安装MacOS虚拟机
date: 2017-08-26 13:33:49
tags: [VMware, macOS]
categories: [自动化测试]
---

## 准备文件

- macOS 操作系统 iso 文件（需要使用 macOS 下载安装文件，使用 [工具](https://github.com/geerlingguy/macos-virtualbox-vm) 转换成 iso）
- Unlocker 2.0.8 [下载地址](http://www.insanelymac.com/forum/files/file/339-unlocker/)
- VMware Workstation 12，player 或 pro 都可以

<!-- more -->

## 安装步骤

1. 安装 VMware

2. 解压下载的 Unlocker，以管理员身份执行文件夹中的 win-install.cmd，解锁创建新虚拟机时的 macOS 选项

  ![image](http://wx4.sinaimg.cn/large/73f872f5gy1fhi1b3ja8ej20dz0byaa9.jpg)

3. 打开 VMware→创建新虚拟机→选择稍后安装操作系统→选择对应的操作系统类型和版本→命名虚拟机和设置虚拟机保存位置→设置磁盘大小→自定义硬件，参数设置参考如下：
   - USB 兼容性选择 USB 2.0（设置为 USB 3.0 可能导致无法在虚拟机和宿主机之间顺利切换 USB 设备）
   - cpu 核心越多越好
   - 内存最好 8G 以上
   - 硬盘 40G 以上
   - CD/DVD 选择 macOS iso 文件

     ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhhe6v3tjej20av05xglm.jpg)

4. 打开虚拟机保存位置，使用文本编辑工具修改文件夹下的 vmx 文件，在末尾添加如下这行配置
   ```
   smc.version = "0"
   ```


5. 播放虚拟机，选择语言并同意条款，来到“选择安装到的磁盘”页面，此时显示只有挂载的 iso，打开磁盘工具

  ![image](http://wx3.sinaimg.cn/large/73f872f5gy1fhheg8eqrqj20cq03y3zb.jpg)

6. 选择名称为 VMware 开头的磁盘，点击抹掉，按默认参数创建磁盘分区，关闭磁盘工具，选择刚才创建的磁盘，点击继续，等待安装完成

  ![image](http://wx4.sinaimg.cn/large/73f872f5gy1fhi0v6rzu0j20ms0bfgnp.jpg)

7. 安装 VMware tools

   ![image](http://wx1.sinaimg.cn/large/73f872f5gy1fhkz6vmpsrj20mn0bjt9d.jpg)
