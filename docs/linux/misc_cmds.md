
### date

```
date -s "20120523 01:01:01" 
ntpdate -u cn.pool.ntp.org 
timedatectl set-timezone Asia/Shanghai

```


### dd

```
sudo dd if=/dev/mmcblk0 of=~/sd-card-copy.img bs=1M status=progress
```