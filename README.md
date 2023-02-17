# install realtek driver

```
sudo apt install --reinstall build-essential bc dkms git
git clone https://github.com/morrownr/8821cu-20210118.git
cd 8821cu-20210118
sudo ./install-driver.sh
sudo modprobe 8821cu
```

## install-driver.sh error: Your Kernel header files aren't properly installed.

Possible reason: You may have recently updated your kernel, but the system is still
using the old kernel's information.


```
$ uname -r
6.0.0-kali3-amd64

$ ls /lib/modules
6.0.0-kali3-amd64   6.0.0-kali6-amd64

```

`6.0.0-kali6-adm64` is the newer kernel image (kali6 > kali3), but the system
is still reporting that it's running `6.0.0-kali3-amd64` as the kernel.

### steps to fix

```
$ sudo update-grub
```

Reboot the system when prompted.

Verify that the system is running the most recent kernel

```
$ uname -r
6.0.0-kali6-amd64
```
