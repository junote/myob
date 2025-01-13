

```
I looked for Debian images [here](https://hub.docker.com/_/debian/tags?page=1&name=bullse), and decided on bullseye-slim:

`docker pull debian:bullseye-slim`

Once

`docker run debian:bullseye-slim`

gets this image running its container ID can be found from a different terminal via

`docker container ls` or `docker ps`

(or, if you are scripting this step, via the command provided in the [Microsoft docu](https://learn.microsoft.com/en-us/windows/wsl/use-custom-distro#export-the-tar-from-a-container)).

`docker export CURRENT_ID > debian11.tar`

created a .tar file from the still running image.

Now calling

`wsl --import Debian11 .\WSLStorage\Debian11\ .\debian11.tar`

from the Windows Powershell successfully imported it to the pre-existing folder `.\WSLStorage\Debian11\`.
```


```
[The top answer](https://askubuntu.com/a/966537/582404) (`ubuntu config --default-user root`) is currently correct for the default instance. But if you have [multiple instances](https://stackoverflow.com/a/60428590/1144854), you need to work slightly harder:

Inside the instance, as root, create/edit `/etc/wsl.conf` and add these lines:

```

```

[user]
default=username
```

```

Then close it, run `wsl --terminate <distro name>` in PowerShell, and restart it.
```



```
sudo dpkg --add-architecture

sudo apt update

sudo apt install libgpiod-dev:arm64
```

#### reference
[linux - How to install Debian 11 on WSL Manually ? I am trying to download it from from Microsoft store but by default it's downloading Debian 12 version - Stack Overflow](https://stackoverflow.com/questions/77170725/how-to-install-debian-11-on-wsl-manually-i-am-trying-to-download-it-from-from)

[How to change default user in WSL Ubuntu bash on Windows 10 - Ask Ubuntu](https://askubuntu.com/questions/816732/how-to-change-default-user-in-wsl-ubuntu-bash-on-windows-10)