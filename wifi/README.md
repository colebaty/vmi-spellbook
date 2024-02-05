# some setup stuff

## FIRST STEPS

Update your box

```bash
$ sudo apt update -y && sudo apt upgrade -y
```

## USB stuff
First you need to pass the USB port through to your VM.  For example, in VirtualBox, Kali VM -> Settings -> USB.
Add new USB filter.



### Check for USB

```bash
# before connecting adapter

$ lsusb # 'list usb'
Bus 001 Device 002: ID 80ee:0021 VirtualBox USB Tablet
Bus 001 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub


# after connecting 

$ lsusb
Bus 001 Device 005: ID 0bda:c811 Realtek Semiconductor Corp. 802.11ac NIC
Bus 001 Device 002: ID 80ee:0021 VirtualBox USB Tablet
Bus 001 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub

```

### Atheros AR9271 or AR7010 devices (ath9k_htc)

It's highly likely these adpaters work 'out of the box.'  If that's the case
for you, you're done.

Otherwise, follow [these instructions](https://wiki.debian.org/ath9k_htc) for installing the drivers on Debian
systems.


### broadcomm/realtek adapters

clone the [github repo](https://github.com/morrownr) which corresponds to the
chipset of your wifi adapter.

for example, if the output of `lsusb` is as follows:

```bash
$ lsusb
Bus 001 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 001 Device 002: ID 80ee:0021 VirtualBox USB Tablet
Bus 001 Device 003: ID 0bda:b812 Realtek Semiconductor Corp. RTL88x2bu [AC1200 Techkey] #<== RTL88x2bu

```

we see the chipset is `RTL88x2bu`, so I would clone the
[88x2bu](https://github.com/morrownr/88x2bu-20210702) repository.


```
sudo apt install --reinstall build-essential bc dkms git
git clone https://github.com/morrownr/<chipset repo>.git
cd <chipset repo dir>
sudo ./install-driver.sh
sudo modprobe <chipset>
```

## errors
### install-driver.sh error: Your Kernel header files aren't properly installed.

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

#### first, try `update-grub`

```bash
$ sudo update-grub
```

Reboot the system when prompted.

Verify that the system is running the most recent kernel

```bash
$ uname -r
6.0.0-kali6-amd64
```

try again

```bash
$ cd /path/to/chipset/repo
$ sudo ./install.sh
```

#### if you're still getting the error, install linux headers

follow [these linuxhint instructions](https://linuxhint.com/install-linux-headers-kali-linux/) to update your linux headers

```bash
$ sudo apt update
$ sudo apt dist-upgrade

# reboot

$ sudo apt install -y linux-headers-$(uname -r)
$ sudo modprobe <chipset>
```

