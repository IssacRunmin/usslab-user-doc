---
sidebar_position: 1
sidebar_label: '连接'
title: '连接'
---

## 连接途径

- 校园网内：服务器登录节点`moon`，IP地址为`10.14.101.139`，端口为`31417`
- 校园网外：需要登录到堡垒机，然后再跳到登录节点。
    - 目前堡垒机IP地址为`43.133.63.249`，端口为`7078`，堡垒机使用rbash（受限的bash，只可ssh）。
    - 需要`ssh key`登录，而不是密码。
    - 如需使用请联系管理员，准备好ssh公钥（第一次使用公钥？本地运行`ssh-keygen`）并发送给管理员进行申请。


## 连接方法

目前支持ssh登录和vscode登录。

- Windows：从 Windows 10 版本 1709 开始，微软在 Windows 系统中默认集成了 OpenSSH 客户端。你可以通过命令提示符或 PowerShell 来使用它
- MacOS 自带 SSH 客户端
- 对于vscode，后续将逐步限制，改为从本网站的vscode web server。
