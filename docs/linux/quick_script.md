
### random mac

```
head -n 1 /dev/random|md5sum |awk '{print $1}'|sed 's/^\(..\)\(..\)\(..\)\(..\)\(..\).*$/00:\1:\2:\3:\4:\5/'
```

