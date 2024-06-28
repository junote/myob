
```
apt install libgpiod
apt install libgpiod2
gpiodetect
gpioinfo
gpioset gpiochip3 9=1
gpioset 3 9=1
gpioset 3 9=0
gpioget gpiochip3 8
gpioget 3 8

echo 44 > /sys/class/gpio/export
echo 44 > /sys/class/gpio/unexport
echo in > /sys/class/gpio/gpio44/direction
cat /sys/class/gpio/gpio44/value
echo 0 > /sys/class/gpio/gpio44/value

```

