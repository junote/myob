### basic 
```
 sudo sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list
sudo apt update
sudo apt dist-upgrade
sudo apt install build-essential
sudo apt install gcc-aarch64-linux-gnu  g++-aarch64-linux-gnu cmake
sudo apt install clang clangd clang-format
sudo apt install openssh*
sudo apt install git

sudo apt install zsh curl wget
sh -c "$(curl -fsSL https://install.ohmyz.sh)"
 ZSH_THEME="ys"
```

### key
```
ssh-keygen -t ed25519 -C "junote"
```

### docker
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io  


sudo apt install iptables 

sudo touch /etc/fstab 

sudo update-alternatives --set iptables /usr/sbin/iptables-legacy 

sudo update-alternatives --set ip6tables /usr/sbin/ip6tables-legacy



sudo usermod -aG docker $USER && newgrp docker


```

```
sudo mkdir /etc/docker/ 

MIRROR=https://dockerproxy.cn

cat <<EOF | sudo tee /etc/docker/daemon.json 
{
"exec-opts": ["native.cgroupdriver=cgroupfs"], 
"log-driver": "json-file", 
"log-opts": { "max-size": "100m" }, 
"storage-driver": "overlay2", 
"registry-mirrors": ["$MIRROR"]
} 
EOF

```

```
sudo /etc/init.d/docker start
docker run hello-world
```


#### debian 11
```
docker pull debian:11
docker run -it debian:11

sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list
apt-get update && apt-get install -y locales && rm -rf /var/lib/apt/lists/* \
	&& localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

apt install build-essential
apt install gcc-aarch64-linux-gnu  g++-aarch64-linux-gnu cmake

docker commit id mydebian11

```

[debian镜像_debian下载地址_debian安装教程-阿里巴巴开源镜像站 (aliyun.com)](https://developer.aliyun.com/mirror/debian?spm=a2c6h.13651102.0.0.3e221b11Cdy4G0)


### vscode
```
clangd # cmake>3.5 add    set(CMAKE_EXPORT_COMPILE_COMMANDS)
clang-format #.clang-format copy from spdlog
clang-tidy #.clang-tidy copy from spdlog

cmake
cmake tools

gitlens

fiteen code
```