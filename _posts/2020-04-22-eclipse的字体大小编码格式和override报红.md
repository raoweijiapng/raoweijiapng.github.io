---
title: eclipse的字体大小，编码格式和Override不支持对接口的实现
tags: eclipse
categories: eclipse
---

* TOC
{:toc}

## eclipse的字体大小

> 改变代码字体,window-preferences-General-Appearance-Colors and Fonts-Basic,选择edit

![字体大小配置1](https:\\raoweijiapng.github.io\static\img\eclipse\字体大小配置1.png)

> 终端输出字体设置,window-preferences-General-Appearance-Colors and Fonts-Terminal Console Font,选择edit

![字体大小配置2](https:\\raoweijiapng.github.io\static\img\eclipse\字体大小配置2.png)

## eclipse的编码格式

> 设置工作空间编码格式,在Window-Preferences-General-Workspace下，面板Text file encoding 选择UTF-8格式

![编码格式配置1](https:\\raoweijiapng.github.io\static\img\eclipse\编码格式配置1.png)

> 设置JSP页面编码格式,在Window-Preferences-Web-JSP Files,面板选择ISO 10646/Unicode(UTF-8)格式编码

![编码格式配置2](https:\\raoweijiapng.github.io\static\img\eclipse\编码格式配置2.png)

> 设置文档编码格式,在Window-Preferences-General-Content Type-Text的最下面设置为编码格式为UTF-8

![编码格式配置3](https:\\raoweijiapng.github.io\static\img\eclipse\编码格式配置3.png)

> 设置项目的文档编码格式,选择项目-右键-Properties-Resource,设置编码为UTF-8格式

![编码格式配置4](https:\\raoweijiapng.github.io\static\img\eclipse\编码格式配置4.png)

## Override不支持对接口的实现

> Windows-Preferences-java-Compiler-compilercompliance level设置成1.8,与本地的jdk版本相同.

![Override不支持实现接口1](https:\\raoweijiapng.github.io\static\img\eclipse\Override不支持实现接口1.png)

> Project-Properties-JavaCompiler-Configure WorkspaceSetting-compilercompliance level

![Override不支持实现接口2](https:\\raoweijiapng.github.io\static\img\eclipse\Override不支持实现接口2.png)

> Project-Properties-Project Facet

![Override不支持实现接口3](https:\\raoweijiapng.github.io\static\img\eclipse\Override不支持实现接口3.png)