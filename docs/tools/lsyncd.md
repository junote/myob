1. create id `ssh-keygen`
2. cope to dst then don't need password `ssh-copy-id test@ip`
3. install lsyncd
4. basic command 
```shell
   
lsyncd -nodaemon -rsync  /home/jcai/tmp/src root@192.168.2.104:/mnt/dst -delay 0.01 -pidfile lsy
ncd.pid

kill $(cat ../lsyncd.pid)   
   
```
5. use file to sync, `lsyncd test.lua`, `kill $(cat ../lsyncd.pid)   `
```lua
settings {
 pidfile = "/home/jcai/tmp/lsyncd.pid",
 logfile = "/home/jcai/tmp/lsyncd.log",
 nodaemon = true,
}
sync {
 default.rsync,
 source = "/home/jcai/tmp/src",
 target = "root@192.168.2.104:/mnt/dst",
 maxDelays = 1,
 delay = 0.001,
-- init = true,
rsync = {
 binary = "/usr/bin/rsync",
 archive = true,
 compress = true,
 bwlimit = 2000
}
}

```


- referece [Lsyncd - Invoking](https://lsyncd.github.io/lsyncd/manual/invoking/)
- 