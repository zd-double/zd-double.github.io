---
layout: post
title:  "Welcome to sofa-pbrpc!"
date:   2016-07-01 13:50:39
categories: jekyll
---
欢迎来到sofa-pbrpc的中文Wiki页面！


sofa-pbrpc(sofa protobuf-based rpc)是使用Boost::Asio实现的基于Google Protocol Buffers RPC框架的网络通信库，在百度公司各部门得到广泛使用。



# 目标

* 轻量
* 易用
* 高性能

# 特性

* 接口简单，容易使用
* 实现高效，性能优异（高吞吐、低延迟、高并发连接数）
* 测试完善，运行稳定
* 支持同步和异步调用，满足不同类型需求
* 支持多级超时设定，灵活控制请求超时时间
* 支持精准的网络流量控制，对应用层透明
* 支持透明压缩传输，节省带宽
* 提供服务和方法级别的服务调用统计信息，方便监控
* 支持自动建立连接和自动重连，用户无需感知连接
* 远程地址相同的Client Stub共享一个连接通道，节省资源
* 空闲连接自动关闭，及时释放资源
* 支持Mock测试
* 支持多Server负载均衡与容错
* 原生支持HTTP协议访问
* 提供内建的Web监控页面
* 提供Python客户端库

# 构建

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/构建指引

# 快速使用

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/快速使用

# 高级使用

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/高级使用

# Python客户端

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/Python客户端

# RPC协议

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/RPC协议

# 性能

参见 https://github.com/BaiduPS/sofa-pbrpc/wiki/性能
