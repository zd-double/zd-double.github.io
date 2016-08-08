---
layout: post
title: 最新功能
date: 2016-08-01 13:50:39
categories: blog 
---

本文档包括：

* WebService
* Profiling

# WebService
 为了能够支持web服务的开发，sofa-pbrpc 引入WebService的功能。WebService使用Web(HTTP)方式接收和响应外部系统的某种请求,从而实现远程调用.  使用webService，用户需要进行以下工作：

 * 定义WebService的处理函数
 WebService的处理函数结构为

 ```c++
 bool ProcFunc(const sofa::pbrpc::HTTPRequest& request, sofa::pbrpc::HTTPResponse& response)
 {
 	...
 }
 ```

 如下面例子所示：

 ```c++
 bool WebServlet(const sofa::pbrpc::HTTPRequest& request, sofa::pbrpc::HTTPResponse& response)
{
    SLOG(INFO, "WebServlet(): request message from %s:%u",
            request.client_ip.c_str(), request.client_port);
    SLOG(INFO, "HTTP-PATH=\"%s\"", request.path.c_str());
    std::map<std::string, std::string>::const_iterator it;
    const std::map<std::string, std::string>& query_params = *request.query_params;
    for (it = query_params.begin(); it != query_params.end(); ++it) {
        SLOG(INFO, "QueryParam[\"%s\"]=\"%s\"", it->first.c_str(), it->second.c_str());
    }
    const std::map<std::string, std::string>& headers = *request.headers;
    for (it = headers.begin(); it != headers.end(); ++it) {
        SLOG(INFO, "Header[\"%s\"]=\"%s\"", it->first.c_str(), it->second.c_str());
    }
    return response.content->Append("<h1>Hello from sofa-pbrpc web server</h1>");
}
 ```
 * 根据处理函数，创建WebService的Servilet。

 ```c++
 sofa::pbrpc::Servlet servlet = sofa::pbrpc::NewPermanentExtClosure(&WebServlet);
 ```

 * 将Servilet注册到某一WebServer的路径下
 
 ```c++
rpc_server.RegisterWebServlet("/hello", servlet);
 ```
 下图为基于WebService实现的sofa-pbrpc监控主页：

![webservice](/static/img/webservice.png)

# Profiling


为分析应用程序各个模块对硬件资源的消耗，方便用户排插性能瓶颈和性能调优，sofa-pbrpc实现了方便快捷的profiling功能。用户只需在应用程序联编 gperftools 库即可查看server的profiling数据，然后通过访问server的监控页面中profiling选项就可以对server程序进行Profiling操作，server端不需要安装graphviz直接在浏览器查看profiling生成的dot图。
用户程序只需做如下操作：
*  联编gperftools的lib库
*  在Makefile中增加-DSOFA-PBRPC_PROFILING的编译选项

最后生成的Profiling视图如下所示：

![profiling](/static/img/profiling.png)


