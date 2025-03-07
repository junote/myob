
### setup basic

1. **检查网络连接状态：**
    
    ```
    ip addr
    ```
    
    这条命令会显示所有网络接口的状态。
    
2. **编辑网络配置文件：** 找到你的网络接口配置文件，通常在 `/etc/sysconfig/network-scripts/` 目录下。文件名通常以 `ifcfg-` 开头，例如 `ifcfg-ens33`。
    
    ```
    cd /etc/sysconfig/network-scripts/
    vi ifcfg-ens33
    ```
    
    将 `ONBOOT` 设置为 `yes`，确保网络接口在启动时自动启用：
    
    ```
    ONBOOT=yes
    ```


### 更换 yum 源

  

1. 可以考虑使用国内的镜像源来替代官方 yum 仓库。例如，阿里云、网易云等都提供了 CentOS 的镜像源。

- 以阿里云为例：

- 备份原有的 yum 配置文件：`sudo cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak`。
- 下载阿里云的 yum 配置文件：`sudo wget -O /etc/yum.repos.d/CentOS-Base.repo [http://mirrors.aliyun.com/repo/Centos-7.repo](http://mirrors.aliyun.com/repo/Centos-7.repo)`。

1. 更新 yum 缓存：`sudo yum makecache`。

**检查 SELinux 和 iptables**

  

1. SELinux 可能会限制 yum 的某些操作。可以尝试将 SELinux 设置为 permissive 模式（临时）来检查是否是 SELinux 导致的问题：`sudo setenforce 0`。如果问题解决，可以进一步调整 SELinux 策略以允许 yum 的正常操作。
2. 检查 iptables 规则，确保没有阻止 yum 相关的网络流量。可以使用 `iptables -L` 命令查看当前的防火墙规则，并根据需要进行调整。

### desktop

CentOS 默认情况下通常不安装桌面环境，特别是在服务器版本中。你可以按照以下步骤来安装 GNOME 桌面环境：

1. **更新系统包：**
    
    ```
    sudo yum update
    ```
    
2. **安装 GNOME 桌面环境及相关工具：**
    
    ```
    sudo yum groupinstall "GNOME Desktop" "Graphical Administration Tools"
    ```
    
3. **设置系统默认运行级别为图形界面模式：**
    
    ```
    sudo systemctl set-default graphical.target
    ```
    
4. **启动图形界面：**
    
    ```
    sudo systemctl start graphical.target
    ```
    
5. **（可选）启用图形登录管理器（适用于 CentOS 7 及更高版本）：**
    
    ```
    sudo systemctl enable gdm
    ```
    
6. **重新启动系统：**
    
    ```
    sudo reboot
    ```

### remote desktop

首先，如果安装测centos7是已经安装了GNOME 或者 KDE 桌面，

则只需要再安装xrdp就可以了。

      直接通过yum install xrdp 是不行的，因为xrdp 不在默认源中

      **先配置源       yum install epel* -y**

      **然后再安装    yum install xrdp 或**   yum --enablerepo=epel -y install xrdp

  其实会发现好多其他方法都要求安装 各种版本的vnc，这是没有必要的，因为你并不使用vncviewer 去远程访问

     安装好 xrdp，后需要**启动 systemctl start xrdp**

      同时需要添加**端口允许访问，xrdp用的端口是 3389**

      或者简单粗暴一点，关闭防火墙，systemctl stop firewalld

      // 其他相关的命令，如：如何设置开机启动 systemctl enable xrdp

                         如何禁用防火墙等；如何添加端口访问等。可再查询

**补充一下 vncserver 和 xrdp的关系**

**通常，为了使用vncserver 我们需要安装** 

            **yum install tigervnc tigervnc-server**

            ****tigervnc tigervnc-server就是提供vncserver的功能的工具。 类似于我们使用word可以选择微软的或者WPS的。 这里我们选择****tigervnc********

****安装好后，我们使用vncserver :1 开启一个id为1桌面****

****xrdp是什么呢? 可以理解为我们使用windows 自带的远程桌面工具去访问 vncserver :1这个桌面。****

****安装好xrdp 和启动了vncserver :1 只需要配置一下相关的接口即可。****