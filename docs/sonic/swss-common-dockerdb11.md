```
git clone --recursive https://github.com/sonic-net/sonic-swss-common
```

```

apt-get update

apt-get install -y make libtool m4 autoconf dh-exec debhelper cmake pkg-config nlohmann-json3-dev \
libhiredis-dev libnl-3-dev libnl-genl-3-dev libnl-route-3-dev libnl-nf-3-dev swig \
libpython2.7-dev libboost-dev libboost-serialization-dev uuid-dev libzmq5 libzmq3-dev

apt-get install -y redis-server redis-tools
apt-get install -y python3-pip
pip3 install pytest
apt-get install -y python
apt-get install cmake libgtest-dev libgmock-dev libyang-dev

cd /usr/src/gtest && sudo cmake . && sudo make


curl -fsSL -o /usr/local/bin/bazel \
https://github.com/bazelbuild/bazel/releases/download/7.4.0/bazel-7.4.0-linux-x86_64
 chmod 755 /usr/local/bin/bazel


apt-get install libnl-3-dev

```



```

    - script: |

        bazel build //...

        bazel test //...

```

### arm64_lib

`sudo dpkg --add-architecture armhf`

`sudo dpkg --add-architecture arm64`

`apt update`

```




```


### card

apt-get install -y  libtool   nlohmann-json3-dev \






