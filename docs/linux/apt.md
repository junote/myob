
## download package

- download required package
  ```shell
apt-cache depends lsyncd
apt download package

sudo apt-get install --reinstall -d `apt-cache depends packname | grep depends | cut -d: f2 |tr -d "<>"`
```