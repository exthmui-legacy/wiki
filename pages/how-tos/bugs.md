---
sidebar: home_sidebar
title: 如何提交错误报告
folder: how-tos
redirect_from: bugreport-howto.html
permalink: /how-to/bugreport
tags:
 - how-to
---

## 以下条件不要提交错误报告
  - 非官方维护版本的exTHmUI 即 [exTHmUI 官方设备](https://download.exthmui.cn/exthmui/) 之外的设备
  - 官方维护列表中无您的设备
  - 寻求安装 exTHmUI 的教程
  - 请求官方设备维护支持
  - 系统特殊功能/特性请求
  - 您修改过系统文件或者更换了内核等
  - 您安装了稀奇古怪的模块或者其他修改

## 报告错误

我们使用此 [模板](https://github.com/exthmui/exthm_issues) 来收集存在问题. 在提交问题之前请先确认其他人是否也报告了此问题, 如果存在, 请避免评论 "我也是!" "什么时候解决?"

我们接受以下错误:

  - [Android](https://github.com/exthmui/exthm_issues)
    - 这些是 exTHmUI 的错误.
    - 例子:
      - 您设备上的指纹传感器无法使用.
      - 当您使用某些系统功能时, 出现崩溃或者未响应.
      - 无法使用 WiFi 通话.

    {% include alerts/important.html content="请不要在同一个问题报告中发布多个问题, 因为错误可能会和多方面因素有关, 比如某些设备上特定的问题, 而有些则不是" %}

    {% include alerts/important.html content="*必须*为您提交的问题报告附加 [Logcat](logcat.html) 并且*必须*在出现问题后立即捕获" %}

  - [Infrastructure](https://gitlab.com/LineageOS/issues/infra)
    - These are bugs in our website and/or software.
    - Examples:
      - The download portal doesn't render something correctly.
      - There's a dead link on the website.
      - An API returns something unexpected.


    {% include alerts/important.html content="If the issue affects your device, this is the **wrong** place to report your issue!" %}

请务必按照要求的格式来填写包含准确信息的模板, 如果不存在准确信息的问题, 我们将关闭此问题申请
