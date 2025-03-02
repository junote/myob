
## example

```
sudo apt-get install libgrpc-dev libgrpc++-dev libprotobuf-dev protobuf-compiler
apt-get install libprotobuf-dev protobuf-compiler

git clone --recurse-submodules -b v1.64.0 --depth 1 --shallow-submodules https://github.com/grpc/grpc

```


```
RUN apt update && apt install -y build-essential autoconf libtool pkg-config git cmake libc-ares-dev libre2-dev libssl-dev zlib1g-dev curl

RUN git clone --recurse-submodules -b v1.39.0-pre1 https://github.com/grpc/grpc && \
    cd /grpc/third_party/abseil-cpp && \
    cmake . && \
    make -j64 install && \
    cd /grpc && mkdir build && cd build && \
    cmake -DgRPC_INSTALL=ON -DgRPC_BUILD_TESTS=OFF .. && \
    make -j64 install && \
    ldconfig

RUN cd / && \
    git clone https://github.com/faaxm/exmpl-cmake-grpc.git && \
    cd exmpl-cmake-grpc && \
    mkdir build && cd build && \
    cmake .. && \
    make -j64
```


### vcpkg

```
vcpkg install grpc

vcpkg install protobuf

```

[advra/grpc-vcpkg-boilerplate-example: This is a simple helloworld example that shows how to setup, configure and compile using vcpkg manager. (github.com)](https://github.com/advra/grpc-vcpkg-boilerplate-example)



#jc : 写一个grpc的基本proto文件，并生成一个cpp的测试程序

#ds : 
### 1. 编写基本的 ` 文件

首先，我们创建一个简单的 proto` 文件，定义了一个 `Greeter` 服务，其中包含一个 `SayHello` 方法。

```proto
// helloworld.proto
syntax = "proto3";

package helloworld;

// The greeting service definition.
service Greeter {
  // Sends a greeting
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// The request message containing the user's name.
message HelloRequest {
  string name = 1;
}

// The response message containing the greetings
message HelloReply {
  string message = 1;
}
```

### 2. 生成 C++ 代码

使用 oc` 编译器生成 C++ 代码。假设你已经安装了 `protoc` 和 `grpc` 插件。

```bash
protoc --cpp_out=. --grpc_out=. --plugin=protoc-gen-grpc=`which grpc_cpp_plugin` helloworld.proto
```

这将生成以下文件：

- oworld.pb.h` 和 `helloworld.pb.cc`：用于处理消息的序列化和反序列化。
- oworld.grpc.pb.h` 和 `helloworld.grpc.pb.cc`：用于处理 gRPC 服务的定义。

### 3. 编写 C++ 测试程序

接下来，我们编写一个简单的 C++ 客户端和服务器来测试这个 Greeter` 服务。

#### 服务器端代码 (.cc`)

```cpp
#include <iostream>
#include <memory>
#include <string>

#include <grpcpp/grpcpp.h>
#include "helloworld.grpc.pb.h"

using grpc::Server;
using grpc::ServerBuilder;
using grpc::ServerContext;
using grpc::Status;
using helloworld::HelloRequest;
using helloworld::HelloReply;
using helloworld::Greeter;

// Logic and data behind the server's behavior.
class GreeterServiceImpl final : public Greeter::Service {
  Status SayHello(ServerContext* context, const HelloRequest* request,
                  HelloReply* reply) override {
    std::string prefix("Hello ");
    reply->set_message(prefix + request->name());
    return Status::OK;
  }
};

void RunServer() {
  std::string server_address("0.0.0.0:50051");
  GreeterServiceImpl service;

  ServerBuilder builder;
  // Listen on the given address without any authentication mechanism.
  builder.AddListeningPort(server_address, grpc::InsecureServerCredentials());
  // Register "service" as the instance through which we'll communicate with
  // clients. In this case it corresponds to an *synchronous* service.
  builder.RegisterService(&service);
  // Finally assemble the server.
  std::unique_ptr<Server> server(builder.BuildAndStart());
  std::cout << "Server listening on " << server_address << std::endl;

  // Wait for the server to shutdown. Note that some other thread must be
  // responsible for shutting down the server for this call to ever return.
  server->Wait();
}

int main(int argc, char** argv) {
  RunServer();
  return 0;
}
```

#### 客户端代码 (.cc`)

```cpp
#include <iostream>
#include <memory>
#include <string>

#include <grpcpp/grpcpp.h>
#include "helloworld.grpc.pb.h"

using grpc::Channel;
using grpc::ClientContext;
using grpc::Status;
using helloworld::HelloRequest;
using helloworld::HelloReply;
using helloworld::Greeter;

class GreeterClient {
 public:
  GreeterClient(std::shared_ptr<Channel> channel)
      : stub_(Greeter::NewStub(channel)) {}

  // Assembles the client's payload, sends it and presents the response back
  // from the server.
  std::string SayHello(const std::string& user) {
    // Data we are sending to the server.
    HelloRequest request;
    request.set_name(user);

    // Container for the data we expect from the server.
    HelloReply reply;

    // Context for the client. It could be used to convey extra information to
    // the server and/or tweak certain RPC behaviors.
    ClientContext context;

    // The actual RPC.
    Status status = stub_->SayHello(&context, request, &reply);

    // Act upon its status.
    if (status.ok()) {
      return reply.message();
    } else {
      std::cout << status.error_code() << ": " << status.error_message()
                << std::endl;
      return "RPC failed";
    }
  }

 private:
  std::unique_ptr<Greeter::Stub> stub_;
};

int main(int argc, char** argv) {
  // Instantiate the client. It requires a channel, out of which the actual RPCs
  // are created. This channel models a connection to an endpoint specified by
  // the argument "--target=" which is the only expected argument.
  std::string target_str("localhost:50051");
  GreeterClient greeter(grpc::CreateChannel(
      target_str, grpc::InsecureChannelCredentials()));
  std::string user("world");
  std::string reply = greeter.SayHello(user);
  std::cout << "Greeter received: " << reply << std::endl;

  return 0;
}
```

### 4. 编译和运行

#### 编译服务器和客户端

```bash
g++ -std=c++11 -o server server.cc helloworld.pb.cc helloworld.grpc.pb.cc -lgrpc++ -lprotobuf -lpthread
g++ -std=c++11 -o client client.cc helloworld.pb.cc helloworld.grpc.pb.cc -lgrpc++ -lprotobuf -lpthread
```

#### 运行服务器

```bash
./server
```

#### 运行客户端

```bash
./client
```

### 5. 预期输出

- 服务器输出：

```
Server listening on 0.0.0.0:50051
```

- 客户端输出：

```
Greeter received: Hello world
```

### 总结

通过以上步骤，你已经成功创建了一个简单的 gRPC 服务，并使用 C++ 编写了客户端和服务器来测试这个服务。你可以根据需要扩展这个示例，添加更多的服务和功能。`client`server``hell`hell`prot``proto



