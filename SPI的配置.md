# SPI的配置

SPI，我配置了三天，最后终于成功了。

这几天我的一些参考链接都在这里：

https://devtalk.nvidia.com/default/topic/1061668/jetson-agx-xavier/spi1-on-xavier/  

https://devtalk.nvidia.com/default/topic/1061177/jetson-agx-xavier/flashing-xavier-with-modified-device-tree/post/5375515/#5375515)  

https://devtalk.nvidia.com/default/topic/1061457/  

https://devtalk.nvidia.com/default/topic/1049488  

https://devtalk.nvidia.com/default/topic/1050427  

https://elinux.org/Jetson/TX2_SPI#Locating_the_SPI_Pins  

https://docs.nvidia.com/jetson/archives/l4t-archived/l4t-322/index.html#page/Tegra%2520Linux%2520Driver%2520Package%2520Development%2520Guide%2Fkernel_custom.html%23wwpID0E0ZC0HA  

https://docs.nvidia.com/jetson/l4t/index.html#page/Tegra%2520Linux%2520Driver%2520Package%2520Development%2520Guide%2Fflashing.html%23wwpID0E0ID0HA  

https://devtalk.nvidia.com/default/topic/1044595/jetson-agx-xavier/question-how-do-i-enable-and-address-spi-ports-/1  

https://devtalk.nvidia.com/default/topic/1042964/jetson-agx-xavier/the-spi-device-can-not-be-used/post/5293576/#5293576  

如果你看了上面的一些链接，你会对Jetson有一些了解，虽然不是很多。如果你不想去看，那就直接开始配置SPI了。

## 刷机

如果你已经刷机结束了，那么主机上(host computer)就会出现Jetpack文件，我的目录如下：

![1](SPI%E7%9A%84%E9%85%8D%E7%BD%AE.assets/1.png)

然后去选择你刷好机的那个目录，我给Xavier刷的是Jetpack4.2,所以选择JetPack_4.2_Linux_P2888这个文件夹，如果你刷的是Jetpack4.2.3,那么选择JetPack_4.2.3_Linux_GA_P2888这个文件夹。接下来的操作都是在这个文件夹中运行的。

## 修改Pinmux

其实我怀疑如果不加这一步可能也可以，因为后面还是要修改Pinmux。为了保险期间，还是加一下。

把这个路径(***JetPack_4.2_Linux_P2888/Linux_for_Tegra/bootloader/t186ref/BCT/tegra19x-mb1-pinmux-p2888-0000-a04-p2822-0000-b01.cfg)***下的这个文件(***tegra19x-mb1-pinmux-p2888-0000-a04-p2822-0000-b01.cfg***)中的这几行修改成如下形式：

```
pinmux.0x0243d040 = 0x00000400; # spi1_sck_pz3: rsvd1, pull-down, tristate-enable, input-enable, lpdr-disable

pinmux.0x0243d020 = 0x00000450; # spi1_miso_pz4: rsvd1, pull-down, tristate-enable, input-enable, lpdr-disable

pinmux.0x0243d058 = 0x00000400; # spi1_mosi_pz5: rsvd1, pull-down, tristate-enable, input-enable, lpdr-disable

pinmux.0x0243d010 = 0x00000400; # spi1_cs0_pz6: rsvd1, pull-up, tristate-enable, input-enable, lpdr-disable

pinmux.0x0243d050 = 0x00000400; # spi1_cs1_pz7: rsvd1, pull-up, tristate-enable, input-enable, lpdr-disable
```



## 修改dtb

1. 去到这个文件路径中***JetPack_4.2_Linux_P2888/Linux_for_Tegra/kernel/dtb/***

2. 反编译dtb为dts

   `dtc -I dtb -O dts -o /tmp/tegra194-p2888-0001-p2822-0000_test.dts tegra194-p2888-0001-p2822-0000.dtb`

3. 修改dts文件

   `gedit /tmp/tegra194-p2888-0001-p2822-0000_test.dts`

4.  修改如下代码段

```
　spi@3210000 {

​                            compatible = "nvidia,tegra186-spi";

​                            reg = <0x0 0x3210000 0x0 0x10000>;

​                            interrupts = <0x0 0x24 0x4>;

​                            \#address-cells = <0x1>;

​                            \#size-cells = <0x0>;

​                            iommus = <0x2 0x20>;

​                            dma-coherent;

​                            dmas = <0x1e 0xf 0x1e 0xf>;

​                            dma-names = "rx", "tx";

​                            spi-max-frequency = <0x3dfd240>;

​                            nvidia,clk-parents = "pll_p", "clk_m";

​                            clocks = <0x4 0x87 0x4 0x66 0x4 0xe>;

​                            clock-names = "spi", "pll_p", "clk_m";

​                            resets = <0x5 0x5b>;

​                            reset-names = "spi";

​                            status = "okay";

​                            linux,phandle = <0x172>;

​                            phandle = <0x172>;

​                            spidev@0 {

​                                          compatible = "spidev";

​                                          reg = <0x0>;

​                                          spi-max-frequency = <0x1312d00>;

​                            };

​              };
```



5. 将dts文件编译为dtb文件

   `dtc -I dts -O dtb -o tegra194-p2888-0001-p2822-0000.dtb /tmp/tegra194-p2888-0001-p2822-0000_test.dts`

6. 然后到这个路径中***Linux_for_Tegra***

   `sudo ./apply_binaries.sh`

## 重新刷机

`sudo ./flash.sh -r jetson-xavier mmcblk0p1`

在这一步之后，Xavier就行相当与重新安装了系统

## 修改Pinmux

Xavier 需要重新修改一下Pinmux

1. 去Xavier的***/dev***路径中查看是否有spidev*文件，例如spidev0.0

2. 如果有，下载devmem2

   `sudo apt-get install devmem2`

   

3. 修改spidev0.0的引脚

   ```
   sudo devmem2 0x0243d040 word 0x00000400 
   sudo devmem2 0x0243d020 word 0x00000458 
   sudo devmem2 0x0243d058 word 0x00000400 
   sudo devmem2 0x0243d010 word 0x00000400 
   sudo devmem2 0x0243d050 word 0x00000400 
   sudo devmem2 0x0c302048 word 0x00000400 
   sudo devmem2 0x0c302050 word 0x00000450 
   sudo devmem2 0x0c302028 word 0x00000400 
   sudo devmem2 0x0c302038 word 0x00000400
   ```
   
   不同的spi需要更改的引脚不同，具体想参考官方文档。

## 然后应该就可以用了
