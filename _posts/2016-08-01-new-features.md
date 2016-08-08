---
layout: post
title: 最新功能
date: 2016-08-01 13:50:39
categories: blog 
---

本文档包括：

* Profiling

# Profiling


为分析应用程序各个模块对硬件资源的消耗，方便用户排插性能瓶颈和性能调优，sofa-pbrpc实现了方便快捷的profiling功能。用户只需在应用程序联编 gperftools 库即可查看server的profiling数据，然后通过访问server的监控页面中profiling选项就可以对server程序进行Profiling操作，server端不需要安装graphviz直接在浏览器查看profiling生成的dot图。
用户程序只需做如下操作：
*  联编gperftools的lib库
*  在Makefile中增加-DSOFA-PBRPC_PROFILING的编译选项

最后生成的Profiling视图如下所示：
![profiling](/static/img/profiling.png)


