
要安装MinGW（Minimalist GNU for Windows），您可以按照以下步骤操作：

1. 访问 MinGW-w64 官方下载页面（推荐使用 MSYS2 方式安装）：
   
   - 直接下载： https://github.com/msys2/msys2-installer/releases/latest
   - 或访问： https://www.msys2.org/
   - [Index of /msys2/distrib/x86_64/ | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/msys2/distrib/x86_64/)
2. 下载并运行MSYS2安装程序
   
   - 选择安装路径（建议使用默认路径）
   - 完成基础安装
3. mirror
```shell
sed -i "s#https\?://mirror.msys2.org/#https://mirrors.tuna.tsinghua.edu.cn/msys2/#g" /etc/pacman.d/mirrorlist*

```
1. 打开MSYS2终端，运行以下命令：
```bash
pacman -Syu                    # 更新系统包
pacman -S mingw-w64-x86_64-gcc # 安装GCC编译器
pacman -S mingw-w64-x86_64-make # 安装Make工具
 ```
```

2. 添加环境变量：
   - 将`D:\msys64\mingw64\bin` 添加到系统的 PATH 环境变量中
   - 重启终端或计算机使环境变量生效
   - 添加变量“MSYS2_PATH_TYPE", value "inherit"
安装完成后，打开命令提示符并输入`gcc --version` 验证安装是否成功。

1. winterminal 

``` cmd
D:\\msys64\\msys2_shell.cmd -msys -defterm -no-start
```


