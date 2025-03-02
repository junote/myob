
### cmake_build

```
cmake -B builddir
cmake --build builddir
cmake --build builddir --target clean
```

### ccache
```
ccache -C
```




### **1. 构建目录相关变量**

|变量名|说明|
|---|---|
|`CMAKE_BINARY_DIR`|构建目录的根目录（即运行 `cmake` 命令的目录）。|
|`CMAKE_CURRENT_BINARY_DIR`|当前处理的 `CMakeLists.txt` 文件对应的构建目录。|
|`CMAKE_SOURCE_DIR`|源代码目录的根目录（即顶层 `CMakeLists.txt` 所在的目录）。|
|`CMAKE_CURRENT_SOURCE_DIR`|当前处理的 `CMakeLists.txt` 文件对应的源代码目录。|
|`CMAKE_INSTALL_PREFIX`|安装目录的根目录（默认值：`/usr/local` 或 `C:/Program Files`）。|

---

### **2. 常用公用变量**

|变量名|说明|
|---|---|
|`CMAKE_BUILD_TYPE`|构建类型（如 `Debug`、`Release`、`RelWithDebInfo`、`MinSizeRel`）。|
|`CMAKE_CXX_COMPILER`|C++ 编译器的路径。|
|`CMAKE_C_COMPILER`|C 编译器的路径。|
|`CMAKE_CXX_FLAGS`|C++ 编译器的全局编译选项。|
|`CMAKE_C_FLAGS`|C 编译器的全局编译选项。|
|`CMAKE_EXECUTABLE_SUFFIX`|可执行文件的后缀（如 `.exe` 在 Windows 上）。|
|`CMAKE_LIBRARY_SUFFIX`|库文件的后缀（如 `.so` 在 Linux 上，`.dylib` 在 macOS 上，`.dll` 在 Windows 上）。|
|`CMAKE_MODULE_PATH`|查找 CMake 模块的路径列表。|
|`CMAKE_PREFIX_PATH`|查找依赖库的路径列表。|
|`CMAKE_RUNTIME_OUTPUT_DIRECTORY`|可执行文件的输出目录。|
|`CMAKE_LIBRARY_OUTPUT_DIRECTORY`|库文件的输出目录。|
|`CMAKE_ARCHIVE_OUTPUT_DIRECTORY`|静态库文件的输出目录。|
|`CMAKE_INCLUDE_PATH`|查找头文件的路径列表。|
|`CMAKE_LIBRARY_PATH`|查找库文件的路径列表。|