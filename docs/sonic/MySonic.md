
### install

### redis
```

# Ubuntu/Debian

sudo apt-get install libhiredis-dev redis-server redis-tools


# Arch

sudo pacman -S hiredis redis-server redis-tools
  

# CentOS/RHEL

sudo yum install hiredis-devel

git clone https://github.com/redis/hiredis.git

cd hiredis

make

sudo make install


 sudo sysctl vm.overcommit_memory=1

sudo systemctl status redis-server
sudo grep -E '^bind|^port' /etc/redis/redis.conf

```


#### zmp
```
sudo apt install libzmq5 libzmq3-dev

sudo pacman install zeromq


```

#### lib
```

# Ubuntu/Debian系统

sudo apt-get install libnl-3-dev libnl-genl-3-dev

  

# CentOS/RHEL系统

sudo yum install libnl3-devel


sudo pacman -S libnl
pacman -Ql libnl | grep netlink.h
```







```
        sudo apt-get install -y make libtool m4 autoconf dh-exec debhelper cmake pkg-config nlohmann-json3-dev \
                         libhiredis-dev libnl-3-dev libnl-genl-3-dev libnl-route-3-dev libnl-nf-3-dev swig3.0 \
                         libpython2.7-dev libboost-dev libboost-serialization-dev uuid-dev libzmq5 libzmq3-dev
        sudo apt-get install -y sudo
        sudo apt-get install -y redis-server redis-tools
        sudo apt-get install -y python3-pip
        sudo pip3 install pytest
        sudo apt-get install -y python
        sudo apt-get install cmake libgtest-dev libgmock-dev libyang-dev

```


