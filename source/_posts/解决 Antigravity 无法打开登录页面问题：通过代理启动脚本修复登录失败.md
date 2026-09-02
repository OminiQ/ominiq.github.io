---
title: Antigravity 无法登录？用代理启动脚本解决
tags:
  - Antigravity
  - 代理
categories:
  - 技术分享
description: 解决 Antigravity 在部分网络环境下无法打开登录页面的问题，通过启动脚本为 Electron 应用注入代理配置。
abbrlink: 2caed138
date: 2026-08-12 19:55:00
---

最近在使用 **Antigravity** 时遇到一个问题：程序启动正常，但登录页面无法加载，OAuth 认证始终无法完成。

## 原因

Antigravity 基于 Electron 框架，内嵌 Chromium 网络组件。**仅设置系统代理不够**——必须在启动时同时设置代理环境变量，并向 Electron 注入 `--proxy-server` 参数，才能让登录请求正常走代理。

## 创建启动脚本

新建 `start-antigravity-with-proxy.vbs`，填入以下内容：

```vb
Option Explicit

Dim objShell, objEnv
Set objShell = CreateObject("WScript.Shell")

' 获取当前进程环境变量集合（作用域仅限当前脚本及衍生的子进程，等同于 setlocal）
Set objEnv = objShell.Environment("PROCESS")

' 设置大写环境变量（兼容常规 Windows 工具及旧版 SDK）
objEnv("HTTP_PROXY") = "http://127.0.0.1:10808"
objEnv("HTTPS_PROXY") = "http://127.0.0.1:10808"
objEnv("ALL_PROXY") = "http://127.0.0.1:10808"
objEnv("NO_PROXY") = "localhost,127.0.0.1,::1"

' 设置小写环境变量（兼容 Linux 移植工具、curl、git、Node.js 及跨平台 CLI）
objEnv("http_proxy") = "http://127.0.0.1:10808"
objEnv("https_proxy") = "http://127.0.0.1:10808"
objEnv("all_proxy") = "http://127.0.0.1:10808"
objEnv("no_proxy") = "localhost,127.0.0.1,::1"

' 组装启动命令与参数
Dim appPath, appArgs, fullCommand
appPath = """C:\Users\你的用户名\AppData\Local\Programs\antigravity\Antigravity.exe"""
appArgs = "--proxy-server=""127.0.0.1:10808"" --proxy-bypass-list=""localhost,127.0.0.1,::1"""
fullCommand = appPath & " " & appArgs

' 启动程序（1 表示正常显示窗口，False 表示不等待程序退出直接结束脚本，等同于 start 命令）
objShell.Run fullCommand, 1, False

Set objEnv = Nothing
Set objShell = Nothing
```

按实际情况修改两处：

- **代理端口**：将 `10808` 替换为你的代理软件实际端口（如 `7890`）；
- **安装路径**：将 `C:\Users\你的用户名\...` 替换为实际的 `Antigravity.exe` 路径（右键快捷方式 → 属性 → 目标 可查看）。

## 使用方法

右键桌面 Antigravity 快捷方式 → 属性，将「目标」修改为：

```text
"C:\Windows\System32\wscript.exe" "C:\Users\xxxx\start-antigravity-with-proxy.vbs"
```

此后直接点击桌面图标即可，VBS 脚本会先注入代理再启动 Antigravity，且不会弹出黑框。若代理配置正确，登录页面应可正常加载。