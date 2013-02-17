---
title: Win8 应用程序开发
layout: post
categories: dev
published: true

---

[使用 C++ 创建你的第一个 Windows 应用商店应用](http://msdn.microsoft.com/zh-CN/library/windows/apps/xaml/hh465045)
开发一款专为 Win8 设计的博客阅读主题（使用 JS）。

花点时间，深入了解一下 [C++/CX](http://msdn.microsoft.com/zh-CN/library/windows/apps/xaml/hh699871)

视图驱动导航比按钮驱动导航要自然，可以表达的信息也更多。

## XAML

XAML（eXtensible Application Markup Language）基于 XML，遵循 XML 语法。

手动编写 XAML 代码，可以更好的熟悉 XAML 结构。

## 思路

从 XML 加载数据，用于 XAML 的数据显示。

VS 2012 可直接读写 XML 文档。

XML 与 yaml 可以互相转换。

### 导航

#### 网格视图

* 目录信息: DirectoryInfo
  * 名称: Name
  * 描述: Description
  * 资源数: ResourceSize
  * 大小: Size
  * 最后更新时间: LastUpdateTime

#### 资源列表视图

* 资源信息: ResourceInfo
  * 图标: Icon
  * 名称: Name
  * 索引值: Index
  * 描述: Description
  * 大小: Size
  * 引用计数: ReferenceCount
  * 最后更新时间: LastUpdateTime


* 资源详细信息
  * 引用列表: ReferenceList

#### 索引列表视图

* 索引信息: IndexInfo
  * 名称: Name
  * 索引值: Index
  * 描述: Description
  * 依赖列表: DependenceList
  * 引用计数: ReferenceCount
  * 最后更新时间: LastUpdateTime


    {% codeblock lang:xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <Champions Title="Champions Overview (of LOL)">
    <Champion Name="Akali" Image="Assets/Images/Akali_Splash_0.jpg"
              Updated="2012-11-03">
      <Content>
        关于该英雄的详细信息。
      </Content>
      <Abilities>
        <Ability Name="Q"/>
        <Ability Name="W"/>
        <Ability Name="E"/>
        <Ability Name="R"/>
      </Abilities>
    </Champion>
    <Champion Name="Annie" Image="Assets/Images/Annie_Splash_0.jpg"
              Updated="2012-11-03">
      <Content>
        关于该英雄的详细信息。
      </Content>
    </Champion>
    <Champion Name="Yeti" Image="Assets/Images/Yeti_Splash_0.jpg"
              Updated="2012-11-03">
      <Content>
        关于该英雄的详细信息。
      </Content>
    </Champion>
    </Champions>
    {% endcodeblock %}

## 异步编程

lambda 表达式

## 任务并行


[将应用程序打包](http://msdn.microsoft.com/zh-cn/library/windows/apps/xaml/hh454036.aspx)

StandardStyles.xaml

默认情况下，可能很多 Style 是被注释了的，需要的话，取消注释即可。

## 访问 SkyDrive

Metro 应用访问 SkeyDrive 等 Live 服务，需要具有应用程序包 ID。

* 唯一的程序包名称和发布者，二者结合称为应用程序包 ID。
* 唯一的标识符，称为客户端 ID。
* 重定向域，这是 API 在需要直接与应用或应用的附属网站交换令牌、数据和消息时可以使用的域。
* 唯一的密码，称为客户端密钥。

## HTTP 基本请求模式

HTTP 协议的一部分：[RFC 2616 Sec.5](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)。

## 访问 GitHub

[设计 Metro 风格的 GitHub for Windows](http://www.oschina.net/question/12_57382)

## 为 GitHub 提供 Web 服务

## JSON
[JSON](http://tools.ietf.org/html/rfc4627)


新浪微博可以一仿。

## coco2d-x
