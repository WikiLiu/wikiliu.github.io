[toc]
# 双物理机使用kgdb调试内核和内核模块

## 设备条件
1. 两台物理机，最好系统版本相同或接近。运行内核的叫target机，打开gdb的叫host。我这里用的ubuntu20.04，gdb版本9.1
2. 串口线，预先知道串口号，例如 /dev/ttyS0 /dev/ttyS1

## 编译内核 target主机上
1. 当前内核配置可以从 /boot/$(uname -r)-config 中看到。ubuntu和debian编译默认开启了kgdb和debug info。重新编译内核是为了获得vmlinux及符号信息。
2. 在ubuntu下有两种方式编译内核，推荐第一种： 
### （一）
```Shell
    mkdir ~/debug-kernel; 
    cd debug-kernel;
    apt source linux;
    cd linux-5.4.0;
    #这一步不是必须 通常默认都是好的 建议check一下 make menucofigure <frame-point debug_info KGDB>
    dpkg-buildpackage -b; #这里如果缺少build库可以执行 sudo apt build-dep linux
```

等待很久后，在~/debug-kernel中有linux-header*、linux-image*-deb、linux-image-*-dbg-deb、linux-libc四个包  #这里最好把source(~/debug-kernel)拷贝到host主机上

```Shell
    sudo dpkg -i linux-image*-deb linux-image-*-dbg-deb linux-header*
```
### （二）
```Shell
    sudo apt search source   # 查看源
    sudo apt install linux-source-* # 选择一个跟原来版本最接近的就行
    cd /usr/src/linux-source-* ;
    su
    make menuconfigure
    make -j12
    make modules_install
    make install
    update-grub
```

### 更改grub启动参数


### 拷贝生成的vmlinux到host主机





## （可选）wsl

### 
	usbip usb-win kernel
###
	pl2303 agent-proxy
### [!拷贝到wsl](https://learn.microsoft.com/zh-cn/windows/wsl/wsl2-mount-disk)
	GET-CimInstance -query "SELECT * from Win32_DiskDrive"
	wsl --mount <DiskPath> --bare
	lsblk  #Get PartitionNumber
	
	wsl --mount <DiskPath> --partition <PartitionNumber> --type <Filesystem>
## （可选）vscode






### 执行步骤
* remote开机
* window共享usb `usbipd  attach --wsl Ubuntu-20.04 --busid 1-5`
* wsl分离串口 `~/Download/agent-proxy-1.97$ ./agent-proxy 5550^5551 0 /dev/ttyUSB0,115200`
* gdb vmlinux

	target remote /dev/ttyUSB0
	lx-symbols /home/rickliu/debug-kernel/linux-5.4.0/arise_kernel
	continue
	
	
	gdb -ex "file vmlinux" -ex "  target remote /dev/ttyUSB0" -ex "lx-symbols /home/rickliu/debug-kernel/linux-5.4.0/arise_kernel" -ex "continue"
	
	
	sudo modprobe drm
	sudo modprobe drm_kms_helper
	sudo insmod /home/rickliu/debug-kernel/linux-5.4.0/arise_kernel/arise_pro.ko
	sleep 3
	sudo systemctl stop gdm3
	sudo rmmod arise_pro
	echo g > /proc/sysrq-trigger    # b 
	sudo insmod /home/rickliu/debug-kernel/linux-5.4.0/arise_kernel/arise_pro.ko
	
	
	
tips：`sudo stty -F /dev/ttyUSB0 115200`  # deal with Ignoring packet error, continuing...
	 














###


    
    
 
    