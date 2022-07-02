---
title: 用markdown写ppt
date: 2022-07-02 09:04:02
tags: ppt
---


# 一、背景

* 经常需要展示工作成果给领导汇报，但是自己不会写ppt，于是乎找到了新的ppt转化方式

* slidev是什么？**概括起来就是slidev是一个前端框架支持将markdown文件转化成很好的ppt格式展示**

  ```html
  Slidev (slide + dev, /slʌɪdɪv/) 是基于 Web 的幻灯片制作和演示工具。它旨在让开发者专注在 Markdown 中编写内容，同时拥有支持 HTML 和 Vue 组件的能力，并且能够呈现像素级完美的布局，还在你的演讲稿中内置了互动的演示样例。
  
  它使用了功能丰富的 markdown 文件来生成精美的幻灯片，具有即时重载的体验。它还拥有很多内置的集成功能，如实时编码、导出 PDF、演讲录制等。由于 Slidev 是由 web 驱动的，因此你可以使用它进行任何操作 —— 具有无限的可能性
  ```

  

# 二、步骤

1. 安装必要的前端软件如下表格

  | 软件                         | 版本号     |
  | ---------------------------- | ---------- |
  | vscode                       | lastest    |
  | nodejs                       | >14 stable |
  | npm i -D playwright-chromium | lastest    |

2. 执行以下命令下载slidev到本地实现初始化

   ```html
   npm init slidev
   ```
3. 将ppt导出pdf功能

   ```html
   npm i -D playwright-chromium
   ```
4. 在终端执行 ppt展示过程快捷键
 

| 按f 进全屏 esc退出             |
| :----------------------------- |
| 按o 查看ppt列表                |
| 按左右/空格键选择上一页/下一页 |

# 三、效果

* http://localhost:3030/

  

# 四、参考

* https://cn.sli.dev/guide/ 

