
```
- vim /etc/docker/daemon.json
- {
    
    "registry-mirrors": [  
    "https://mirror.ccs.tencentyun.com"  
    ]  
    }  
    
- sudo systemctl restart docker
- docker pull nagui/opengrok
- docker run -d -p 8080:8080 -v /home/jcai/thanos/chassisx-g30/src:/src --name opengrok nagui/opengrok

```