[vcpkg documentation | Microsoft Learn](https://learn.microsoft.com/en-us/vcpkg/)
## install

```
git clone https://github.com/microsoft/vcpkg.git 3rd/vcpkg
sudo apt install curl zip unzip tar ninja-build
cd 3rd/vcpkg && ./bootstrap-vcpkg.sh
export VCPKG_ROOT=/install_dir/vcpkg
export PATH=$PATH:$VCPKG_ROOT
```

### lib install
```
vcpkg install spdlog:x64-linux

vcpkg install spdlog:arm64-linux
```

### cmake_CmakeLists.txt
- **cmake need to before project**
```
cmake_minimum_required(VERSION 3.20)

set(CMAKE_TOOLCHAIN_FILE "/vcpkg_install_dir/vcpkg/scripts/buildsystems/vcpkg.cmake")
set(VCPKG_CHAINLOAD_TOOLCHAIN_FILE "${CMAKE_CURRENT_SOURCE_DIR}/arm64-gcc.cmake")
set(VCPKG_TARGET_TRIPLET "arm64-linux")

project(mydev)

add_executable(mydev main.cpp)

find_package(spdlog CONFIG REQUIRED)
find_package(cli CONFIG REQUIRED)

target_link_libraries(mydev PRIVATE spdlog::spdlog cli::cli)
```

```
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm64)

SET(CMAKE_CXX_COMPILER /usr/bin/aarch64-linux-gnu-g++)
SET(CMAKE_C_COMPILER /usr/bin/aarch64-linux-gnu-gcc)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pthread")

# adjust the default behavior of the FIND_XXX() commands:
# search programs in the host environment
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)

# search headers and libraries in the target environment
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
```

### cmake_presets

In vscode, can use left cmake pannel to compile, no need commands
```
cmake --list-presets

cmake --preset=x86

cmake --build build
```


```
{
    "version": 8,
    "configurePresets": [
        {
            "name": "x86",
            "displayName": "x86-linux",
            "description": "x86-linux",
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/build/${presetName}",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug",
                "VCPKG_HOST_TRIPLET": "x64-linux",
                "CMAKE_TOOLCHAIN_FILE": "/home/jcai/gitee/mydev/3rd/vcpkg/scripts/buildsystems/vcpkg.cmake"
            }
        },
        {
          "name": "arm",
          "displayName": "arm-linux",
          "description": "arm-linux",
          "generator": "Ninja",
          "binaryDir": "${sourceDir}/build/${presetName}",
          "cacheVariables": {
              "CMAKE_BUILD_TYPE": "Debug",
              "VCPKG_CHAINLOAD_TOOLCHAIN_FILE": "${sourceDir}/cmake/arm64-linux-gcc.cmake",
              "VCPKG_TARGET_TRIPLET": "arm64-linux",
              "CMAKE_TOOLCHAIN_FILE": "/home/jcai/gitee/mydev/3rd/vcpkg/scripts/buildsystems/vcpkg.cmake"

          }
      }
    ]
}
```
