# Official-Driver-for-UGREEN-AC1300-RTL8812AU-on-ubuntu20.04-with-kernel-5.13.0
Install guide for UGREEN AC1300 with RTL8812AU on ubuntu20.04 Linux kernel 5.13.0-1009-oem

# Downgrade kernel of the linux
For official driver support linux kernel 2.6.18~5.13, so you need satisfy the prerequisite. A website can be find here [UBUNTU20.04降低内核方法](https://blog.51cto.com/u_745171/12930083).
5.13.0-1009-oem has been tested.

# Install guide
## 1 Some prerequisite
```
sudo apt-get update
sudo apt-get install build-essential
sudo apt-cache search linux | grep linux-image
```
Find a kernel between 2.6.18~5.13 which your linux support. linux-image-5.13.0-1009-oem was tested on my system(ubuntun 20.04.6).
```
sudo apt install linux-image-5.13.0-1009-oem
sudo apt-get install linux-headers-$(uname -r)
grep menuentry /boot/grub/grub.cfg
sudo gedit /etc/default/grub
```
Modify the text as following:
```
# GRUB_DEFAULT=0
GRUB_DEFAULT="gnulinux-advanced-ee85522d-7231-4125-9bf0-0c2d7f0e69c6>gnulinux-5.13.0-1009-oem-advanced-ee85522d-7231-4125-9bf0-0c2d7f0e69c6"
```
```
sudo update-grub
```
Makesure there is no any worning on output.
## 2 install driver
Git clone this repository.
```
cd RTL8812AU_Linux_v5.13.6-15-gc40b977e2.20210629
sudo chmod a+x install.sh
sudo sh install.sh
```
Then, Replug the USB network adapter, and wifi will be ready in your system setting.
# P.s. Based on this website [Fix for compilation error for linux kernel >=5.12 because of GRO_DROP deprecation](https://github.com/aircrack-ng/rtl8814au/pull/73/files)
File `recv_linux.c` under path RTL8812AU_Linux_v5.13.6-15-gc40b977e2.20210629/driver/rtl8812AU_linux_v5.13.6-15-gc40b977e2.20210629/os_dep/linux was modified.
```
#ifdef CONFIG_RTW_GRO
		if (pregistrypriv->en_gro) {
			#if (LINUX_VERSION_CODE >= KERNEL_VERSION(5, 12, 0))
			rtw_napi_gro_receive(&padapter->napi, pskb);
			rx_ok = _TRUE;
			#else
			if (rtw_napi_gro_receive(&padapter->napi, pskb) != GRO_DROP)
				rx_ok = _TRUE;
			#endif
			goto next;
		}
#endif /* CONFIG_RTW_GRO */
```
