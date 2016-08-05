---
layout: post
title: 安装
date: 2016-07-01 9:50:39
categories: blog 
---

本文档指引你如何构建sofa-pbrpc，包含：

* 编译依赖
* 编译
* 安装
* 单元测试
* 使用样例

#  编译依赖

sofa-pbrpc库的构建依赖boost-1.53.0、protobuf-2.4.1、snappy、zlib。

##  Boost

下载地址：http://downloads.sourceforge.net/project/boost/boost/1.53.0/boost_1_53_0.tar.gz

编译：因为只需要boost的头文件，所以解压即可，无需编译。

配置：修改“depends.mk”，将BOOST_HEADER_DIR设置为解压得到的boost文件夹路径，譬如：

BOOST_HEADER_DIR=/home/users/qinzuoyan01/libs/boost_1_53_0

##  Protobuf

下载地址：http://protobuf.googlecode.com/files/protobuf-2.4.1.tar.gz

编译：

解压，cd到解压得到的文件夹下；
./configure –prefix=$PWD/output
make
make install
配置：修改“depends.mk”，将PROTOBUF_DIR设置为编译输出文件夹路径，譬如：

PROTOBUF_DIR=/home/users/qinzuoyan01/libs/protobuf-2.4.1/output

##  Snappy

下载地址：http://snappy.googlecode.com/files/snappy-1.1.1.tar.gz

编译：

解压，cd到解压得到的文件夹下；
./configure –prefix=$PWD/output
make
make install
配置：修改“depends.mk”，将SNAPPY_DIR设置为编译输出文件夹路径，譬如：

SNAPPY_DIR=/home/users/qinzuoyan01/libs/snappy-1.1.1/output

##  Zlib

如果你的系统库中已经安装zlib，请直接跳过。

下载地址：http://zlib.net/zlib-1.2.8.tar.gz

#  编译：

解压，cd到解压得到的文件夹下；
./configure –prefix=$PWD/output
make
make install
配置：修改“depends.mk”，将ZLIB_DIR设置为编译输出文件夹路径，譬如：

ZLIB_DIR=/home/users/qinzuoyan01/libs/zlib-1.2.8/output
编译

运行“make”进行编译。

#  安装

运行“make install”进行安装。

默认会安装到“./output”文件夹下，可通过修改“Makefile”文件中的“PREFIX”来设置安装路径。

安装的文件包括：

include/*
lib/libsofa-pbrpc.a
bin/sofa-pbrpc-client
#  单元测试

进入单元测试文件夹“unit-test”。

依赖：单元测试需额外依赖gtest库，下载地址为 http://googletest.googlecode.com/files/gtest-1.7.0.zip ，解压即可，无需编译。然后修改文件“unit-test/depends.mk”，将GTEST_DIR设置为解压得到的gtest文件夹路径。

编译：运行“make”进行编译。

测试：运行“sh run_test.sh”执行测试。

#  使用样例

进入样例文件夹“sample”。

使用样例共有四个：echo/timeout_sample/compress_sample/mock_sample。

除了mock_sample，其他三个样例可直接进入文件夹运行“make”。

mock_sample需要指定gtest依赖，配置方式与“unit-test”相同。

