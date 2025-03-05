
### dtc

```
DTB --》 DTS

./dtc -I dtb -O dts *.dtb -o *.dts

 
DTS -》 DTB

./dtc -I dts -O dtb -o test.dtb test.dts

```

### wdg

```
+	gpiowdt: watchdog {
+		compatible = "linux,wdt-gpio";
+		gpios = <&gpio1 5 GPIO_ACTIVE_LOW>;
+		hw_algo = "level";
+		hw_margin_ms = <1600>;
+	};

```

