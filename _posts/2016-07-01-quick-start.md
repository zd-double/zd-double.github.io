---
layout: post
title: Quick Start  
date:   2016-07-01 13:50:39
categories: others
---

# 快速使用三部曲

使用sofa-pbrpc只需要三步：

* 定义通讯协议

* 实现Server

* 实现Client

样例代码参见“sample/echo”。

## 定义通讯协议

定义协议只需要编写一个proto文件即可。

范例：echo_service.proto
```c++
package sofa.pbrpc.test;
option cc_generic_services = true;

// 定义请求消息
message EchoRequest {
    required string message = 1;
}
// 定义回应消息
message EchoResponse {
    required string message = 1;
}

// 定义RPC服务，可包含多个方法（这里只列出一个）
service EchoServer {
    rpc Echo(EchoRequest) returns(EchoResponse);
}
```

使用protoc编译'echo_service.proto'，生成接口文件'echo_service.pb.h'和'echo_service.pb.cc'。

>**注意：**
>
>package会被映射到C++中的namespace，为了避免冲突建议使用package；
>需要设置“cc_generic_services”，以通知protoc工具生成RPC框架代码；
>这里EchoRequest和EchoResponse的成员完全相同，在实际应用中可以设置不同的成员；

## 实现Server

### 头文件
```c++
 #include <sofa/pbrpc/pbrpc.h>  // sofa-pbrpc头文件
 #include "echo_service.pb.h"   // service接口定义头文件
 ```

### 实现服务
```c++
class EchoServerImpl : public sofa::pbrpc::test::EchoServer
{
public:
    EchoServerImpl() {}
    virtual ~EchoServerImpl() {}

private:
    virtual void Echo(google::protobuf::RpcController* controller,
                      const sofa::pbrpc::test::EchoRequest* request,
                      sofa::pbrpc::test::EchoResponse* response,
                      google::protobuf::Closure* done)
    {
        sofa::pbrpc::RpcController* cntl =
            static_cast<sofa::pbrpc::RpcController*>(controller);
        SLOG(NOTICE, "Echo(): request message from %s: %s",
            cntl->RemoteAddress().c_str(), request->message().c_str());
        response->set_message("echo message: " + request->message());
        done->Run();
    }
};
```
>**注意：**
>服务完成后必须调用done->Run()，通知RPC系统服务完成，触发发送Response；
>在调了done->Run()之后，Echo的所有四个参数都不再能访问；
>done-Run()可以分派到其他线程中执行，以实现了真正的异步处理；
>注册和启动服务

```c++
int main()
{
    // 设置日志级别
    SOFA_PBRPC_SET_LOG_LEVEL(NOTICE);
    
    // 定义RpcServer
    sofa::pbrpc::RpcServerOptions options;
    options.work_thread_num = 8;
    sofa::pbrpc::RpcServer rpc_server(options);

    // 启动RpcServer
    if (!rpc_server.Start("0.0.0.0:12321")) {
        SLOG(ERROR, "start server failed");
        return EXIT_FAILURE;
    }

    // 创建和注册服务
    sofa::pbrpc::test::EchoServer* echo_service = new EchoServerImpl();
    if (!rpc_server.RegisterService(echo_service)) {
        SLOG(ERROR, "register service failed");
        return EXIT_FAILURE;
    }

    // 等待SIGINT/SIGTERM退出信号
    rpc_server.Run();

    // 停止Server
    rpc_server.Stop();

    return EXIT_SUCCESS;
}
```

## 实现Client

Client支持同步和异步两种调用方式：

同步调用时，调用线程会被阻塞，直到收到回复或者超时；
异步调用时，调用线程不会被阻塞，收到回复或者超时会调用用户提供的回调函数；

### 头文件
```c++
 #include <sofa/pbrpc/pbrpc.h>  // sofa-pbrpc头文件
 #include "echo_service.pb.h"   // service接口定义头文件
```
### 同步调用

```c++
int main()
{
    SOFA_PBRPC_SET_LOG_LEVEL(NOTICE);

    // 定义RpcClient对象，管理RPC的所有资源
    // 通常来说，一个client程序只需要一个RpcClient实例
    // 可以通过RpcClientOptions指定一些配置参数，譬如线程数、流控等
    sofa::pbrpc::RpcClientOptions client_options;
    client_options.work_thread_num = 8;
    sofa::pbrpc::RpcClient rpc_client(client_options);

    // 定义RpcChannel对象，代表一个消息通道，需传入Server端服务地址
    sofa::pbrpc::RpcChannel rpc_channel(&rpc_client, "127.0.0.1:12321");
 
    // 定义EchoServer服务的桩对象EchoServer_Stub，使用上面定义的消息通道传输数据
    sofa::pbrpc::test::EchoServer_Stub stub(&rpc_channel);
 
    // 定义和填充调用方法的请求消息
    sofa::pbrpc::test::EchoRequest request;
    request.set_message("Hello world!");
 
    // 定义方法的回应消息，会在调用返回后被填充
    sofa::pbrpc::test::EchoResponse response;
 
    // 定义RpcController对象，用于控制本次调用
    // 可以设置超时时间、压缩方式等；默认超时时间为10秒，默认压缩方式为无压缩
    sofa::pbrpc::RpcController controller;
    controller.SetTimeout(3000);
 
    // 发起调用，最后一个参数为NULL表示为同步调用
    stub.Echo(&controller, &request, &response, NULL);

    // 调用完成后，检查是否失败
    if (controller.Failed()) {
        // 调用失败后的错误处理，譬如可以进行重试
        SLOG(ERROR, "request failed: %s", controller.ErrorText().c_str());
    }

    return EXIT_SUCCESS;
}
```

### 异步调用

```c++
// 定义回调函数
void EchoCallback(sofa::pbrpc::RpcController* cntl,
        sofa::pbrpc::test::EchoRequest* request,
        sofa::pbrpc::test::EchoResponse* response,
        bool* callbacked)
{
    // 打印一些日志
    SLOG(NOTICE, "RemoteAddress=%s", cntl->RemoteAddress().c_str());
    SLOG(NOTICE, "IsRequestSent=%s", cntl->IsRequestSent() ? "true" : "false");
    if (cntl->IsRequestSent())
    {
        SLOG(NOTICE, "LocalAddress=%s", cntl->LocalAddress().c_str());
        SLOG(NOTICE, "SentBytes=%ld", cntl->SentBytes());
    }

    // 检查调用是否失败
    if (cntl->Failed()) {
        SLOG(ERROR, "request failed: %s", cntl->ErrorText().c_str());
    }
    else {
        SLOG(NOTICE, "request succeed: %s", response->message().c_str());
    }

    // 可以在回调函数中执行一些释放资源的操作
    delete cntl;
    delete request;
    delete response;

    *callbacked = true;
}

int main()
{
    SOFA_PBRPC_SET_LOG_LEVEL(NOTICE);

    // 定义RpcClient
    sofa::pbrpc::RpcClientOptions client_options;
    sofa::pbrpc::RpcClient rpc_client(client_options);

    // 定义RpcChannel
    sofa::pbrpc::RpcChannel rpc_channel(&rpc_client, "127.0.0.1:12321");

    // 定义EchoServer服务的桩对象
    sofa::pbrpc::test::EchoServer_Stub stub(&rpc_channel);

    // 创建Request和Response消息
    sofa::pbrpc::test::EchoRequest* request = new sofa::pbrpc::test::EchoRequest();
    request->set_message("Hello from qinzuoyan01");
    sofa::pbrpc::test::EchoResponse* response = new sofa::pbrpc::test::EchoResponse();

    // 创建RpcController
    sofa::pbrpc::RpcController* cntl = new sofa::pbrpc::RpcController();
    cntl->SetTimeout(3000);

    // 创建回调函数，回调函数可以预绑定一些参数，譬如callbacked
    bool callbacked = false;
    google::protobuf::Closure* done = sofa::pbrpc::NewClosure(
            &EchoCallback, cntl, request, response, &callbacked);

    // 发起调用，最后一个参数非NULL表示为异步调用
    stub.Echo(cntl, request, response, done);

    // 等待回调完成，此处为简单的变量探测方式，不推荐
    while (!callbacked) {
        usleep(100000);
    }

    return EXIT_SUCCESS;
}
```

>**注意：**
>
>异步调用传入的controller、request、response参数，在回调函数执行之前需一直保持有效；
>回调函数的执行会分配到专门的回调线程中运行，可以通过设置RpcClientOptions的callback_thread_num来配置回调线程数；
