
#### mirror

```
cp -a /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.bak

找到第一个 Server=... 的行在该行上一行写入以下内容 Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch # 中国科学技术大学开源镜像站 Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch # 清华大学开源软件镜像站 Server = https://repo.huaweicloud.com/archlinux/$repo/os/$arch # 华为开源镜像站 Server = http://mirror.lzu.edu.cn/archlinux/$repo/os/$arch # 兰州大学开源镜像站 Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch # 阿里云镜像站

更新软件包
pacman -Syy
```
#### pacman

```

# 1.系统更新
#   对整个archLinux进行更新
pacman -Syu
#   如果你已经使用 pacman -Sy 将本地的包数据库与远程的仓库进行了同步,也可以只执行:
pacman -Su
#   如提示冲突，可强制更新(dd跳过所有检查)
pacman -Syudd

# 2.安装包
pacman -S 包名                   # 你也可以同时安装多个包, 只需以空格分隔包名即可
pacman -Sy 包名                  # 与上面命令不同的是,该命令将在同步包数据库后再执行安装
pacman -Sv 包名                  # 在显示一些操作信息后执行安装
pacman -U  包名.tar.gz           # 安装本地包,其扩展名为 pkg.tar.gz

# 3.删除包
pacman -R 包名                   # 该命令将只删除包,不包含该包的依赖
pacman -Rs 包名                  # 在删除包的同时,也将删除其依赖
pacman -Rd 包名                  # 在删除包时不检查依赖

# 4.搜索包
pacman -Ss 关键字                # 这将搜索含关键字的包
pacman -Qi 包名                  # 查看有关包的信息
pacman -Ql 包名                  # 列出该包的文件

# 5.其他用法
sudo pacman -R $(pacman -Qdtq)  # 清除系统中无用的包
pacman -Sw 包名                  # 只下载包,不安装
pacman -Sc                      # Pacman下载的包文件位于 /var/cache/pacman/pkg/ 目录
                                # 该命令将清理未安装的包文件
pacman -Scc                     # 清理所有的缓存文件


```

#### aur
官方仓库的软件实在是太少了， AUR（Arch 用户软件仓库，全称 Arch User Repository）里有许多软件。AUR 是为用户而建、由用户主导的 Arch 软件仓库。
```
sudo pacman -S yay

# 安装 AUR 软件 
yay -S 包名 

# 使用 yay 安装下面软件之前，请先确保基础打包工具（base-devel）已经安装

yay -S base-devel
```



```
sudo pacman -S helix
sudo pacman -S openssh
sudo pacman -S iputils
sudo pacman -S clang clangd clang-format git zsh curl weget ripgrep
sudo pacman -S clang
sudo pacman -S clangd
sudo pacman -S zsh curl wget ripgrep
helix --health
sudo pacman -S ruff
sh -c "$(curl -fsSL https://install.ohmyz.sh)"
```