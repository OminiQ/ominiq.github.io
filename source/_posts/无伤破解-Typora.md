---
title: 无伤破解 Typora
tags: Typora
abbrlink: 577909b
date: 2025-03-07 06:44:54
---

Typora 是一款优秀的 Markdown 编辑器，但其付费激活机制可能会对部分用户造成困扰。本文将分享如何通过修改程序文件来破解 Typora，并去除激活弹窗。**请注意，本文仅供学习交流使用，请支持正版软件！**


## 破解激活步骤


1. 进入 Typora 的安装路径，在该目录下，找到以 `LicenseIndex` 开头的 `.js` 文件。

  <Typora安装路径>\Typora\resources\page-dist\static\js\

2. 使用文本编辑器（如记事本）打开上述 `LicenseIndex` 文件，使用 `Ctrl + H` 进行替换操作，按以下方式进行：

- **搜索：**  
  ```js
  hasActivated="true"==e.hasActivated
  ```

- **替换为：**  
  ```js
  hasActivated="true"=="true"
  ```

3. 保存修改后的文件并退出编辑器。
4. 重新启动 Typora，您会发现激活提示已成功消失。


## 去除激活弹窗


1. 前往安装目录下的以下路径：

  <Typora安装路径>Typora\resources\page-dist\license.html

2. 使用文本编辑器打开 `license.html` 文件。
3. `Ctrl + A` 全选文件中的内容并删除，粘贴以下代码到文件中，保存后退出编辑器。

```html
<!doctype html>
<html lang="en">
<body onload='setTimeout(mm, 1)'>
<script>
function mm(){
    if (window.opener) {
        window.opener = null;
    }
    window.close();
}
</script>
</body>
</html>
```