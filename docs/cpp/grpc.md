
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

