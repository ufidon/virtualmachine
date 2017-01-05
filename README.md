# virtualmachine
虚拟机：仿真PC、嵌入式系统

# 参考链接群

* http://wiki.qemu.org/Main_Page
* https://en.wikibooks.org/wiki/QEMU
* http://ronubo.blogspot.com/2016/02/u-boot-over-qemu-arm.html
* https://wiki.ubuntu.com/Kernel/Dev/QemuARMVexpress

## 虚拟机玩嵌入式linux

* ctr+a x退出虚拟机

### 运行u-boot
1. 下载qemu 2.8源码, 编译并安装
2. 下载u-boot, 配置vexpress_ca9x4_defconfig, 编译
3. 用qemu运行u-boot: qemu-system-arm -M vexpress-a9 -nographic -kernel u-boot

### 用gdb调试:
1. 用qemu运行u-boot: qemu-system-arm -M vexpress-a9 -nographic -kernel u-boot -s -S
程序将停于复位向量处
2. 用gdb调试: arm-linux-gnueabihf-gdb u-boot
在gdb内连接qemu的调试服务: target remote :1234 即可看到
_start () at ../arch/arm/lib/vectors.S:54
54		b	reset

### 用TFTP下载内核
#### TFTP安装与调试
 1. sudo apt install tftpd-hpa
 2. sudo service tftpd-hpa status
 3. sudo chown -R tftp /var/lib/tftpboot
 4. sudo usermod -a -G tftp $USER
 5. touch /var/lib/tftpboot/test.txt
 6. echo 'hello' > /var/lib/tftpboot/test.txt
 7. tftp localhost
 参考链接群:
 
* https://help.ubuntu.com/community/TFTP

#### TUN/TAP安装

* 安装TAP设备
 1. sudo mkdir /dev/net
 2. sudo mknod /dev/net/tun c 10 200
 3. sudo /sbin/modprobe tun
 
* 载卸TAP的脚本
 1. sudo mv /etc/qemu-ifup /etc/_qemu-ifup
 2. sudo mv /etc/qemu-ifdown /etc/_qemu_down
 3. sudo vim /etc/qemu-ifup
 4. sudo vim /etc/qemu-ifdown
 5. sudo chmod +x /etc/qemu-if*
 6. sudo /opt/dev/qemu/bin/qemu-system-arm -M vexpress-a9 -cpu cortex-a9 -kernel ./u-boot/ovex/u-boot -nographic -append "console=tty0 console=ttyAMA0 rw" -net nic -net tap,ifname=tap0
 
#### TFTP下载内核
 1. u-boot 环境变量设置:
  * sete ipaddr 192.168.2.2
  * sete serverip 192.168.2.1
 2. 下载内核及设备树
  * tftp 0x61000000 zImage
  * tftp 0x62000000 vexpress-v2p-ca9.dtb
 3. 运行内核
  * bootz 0x61000000 - 0x62000000

参考链接群:

* http://elinux.org/Virtual_Development_Board
* https://www.kernel.org/doc/Documentation/networking/tuntap.txt
* https://en.wikibooks.org/wiki/QEMU/Networking
* https://people.gnome.org/~markmc/qemu-networking.html

#### 用忙盒(BusyBox) 制作极小根文件系统
 1. 安装并配置网络文件系统(NFS)
 2. 编译忙盒
 3. 手工制作极小根文件系统

参考链接群:

* https://help.ubuntu.com/community/NFSv4Howto
* https://help.ubuntu.com/community/SettingUpNFSHowTo
