---
layout: post
title:  "welcome-to-sofa-pbrpc!"
date:   2016-07-01 8:50:39
categories: blog
---

# Welcome to the sofa-pbrpc Wiki page!

[**sofa-pbrpc**](https://github.com/baidu/sofa-pbrpc) is a light-weight RPC implement of google protobuf RPC framework.

Wiki: [https://github.com/baidu/sofa-pbrpc/wiki](https://github.com/baidu/sofa-pbrpc/wiki)

[View original](https://github.com/baidu/sofa-pbrpc)

# Features

* High performace.
* Easy to use. Refer to sample codes in ['./sample'](https://github.com/baidu/sofa-pbrpc/tree/master/sample).
* Support sync call and async call. Refer to ['./sample/echo'](https://github.com/baidu/sofa-pbrpc/tree/master/sample/echo).
* Support three level (service/method/request) timeout. Refer to ['./sample/timeout_sample'](https://github.com/baidu/sofa-pbrpc/tree/master/sample/timeout_sample).
* Support transparent compression. Refer to ['./sample/compress_sample'](https://github.com/baidu/sofa-pbrpc/tree/master/sample/compress_sample).
* Support mock test. Refer to ['./sample/mock_sample'](https://github.com/baidu/sofa-pbrpc/tree/master/sample/mock_sample).
* Support network flow control.
* Support auto connecting and reconnecting.
* Support keep alive time of idle connections.
* Support statistics for profiling.
* Support multi-server load balance and fault tolerance.
* Support http protocol.
* Provide web monitor.
* Provide python client library.
* Provide WebService to implement a web server. 
* Provide Profiling.

# Dependings

This lib depends on boost-1.53.0 (only need header), protobuf-2.4.1, snappy and zlib:

* boost - http://www.boost.org/
* protobuf - http://code.google.com/p/protobuf/
* snappy - http://code.google.com/p/snappy/
* zlib - http://zlib.net/

> **ATTENTION:**
> 
> 1.  boost header is only needed when compiling the lib, but no need for user code.google.com
> 
> 2.  Extrally, './unit-test' and './sample/mock_sample' also depends on gtest:
        gtest - http://code.google.com/p/googletest/

# Build

1. Modify the file ['./depends.mk'](https://github.com/baidu/sofa-pbrpc/blob/master/depends.mk) to specify depending libs,
   	The necessary libs is boost, protobuf, snappy, and zlib.
2. Run 'make' to build sofa-pbrpc.
	The default optimization level is 'O2',
	To change it, modify the 'OPT' variable in file['./Makefile'](https://github.com/baidu/sofa-pbrpc/blob/master/Makefile).
3. Run 'make install' to install sofa-pbrpc,
	The default install directory is './output'.
	To change it, modify the 'PREFIX' variable in file ['./Makefile'](https://github.com/baidu/sofa-pbrpc/blob/master/Makefile).
For more details, please refer to the wiki.


# Sample

For sample codes, please refer to ['./sample'](https://github.com/baidu/sofa-pbrpc/tree/master/sample) and the wiki.


# Support

[opensearch@baidu.com](opensearch@baidu.com)
