#### **1. 使用 RTC 作为时间源（`ntpd`）**
```
# /etc/ntp.conf
server 127.127.1.0 iburst   # 关键：使用本地时钟驱动
fudge 127.127.1.0 stratum 8  # 设置 stratum 避免被拒绝
```


#### **2. 验证是否生效**

ntpq -pn

**预期输出：**

Copy

Download

     remote           refid      st t when poll reach   delay   offset  jitter
==============================================================================
*127.127.1.0     .LOCL.           8 l   12   64  377    0.000    0.000   0.000

- `*` 表示当前同步源。
    
- `refid=.LOCL.` 表示本地时钟。
    
- `st=8` 表示 stratum 8（有效时间源）。


#### **客户端强制同步（临时方案）**

如果急需同步，可在客户端强制步进时间：


ntpdate -u -b 10.10.30.10  # -u: 绕过端口限制 -b: 强制步进时间

