
```
 wsl --update
wsl --install archlinux
wsl --list

wsl --terminate arch
```



```
[experimental]
autoMemoryReclaim=gradual  
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true

[wsl2]
kernelCommandLine = cgroup_no_v1=all systemd.unified_cgroup_hierarchy=1
guiApplications = true

```