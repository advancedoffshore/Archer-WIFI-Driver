## RTL8812AU/21AU and RTL8814AU drivers
### Supports Realtek 8811, 8812, 8814 and 8821 chipsets

### DKMS
This driver can be installed using [DKMS]. This is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the `dkms` package, which on Debian (based) systems is done like this:
```
sudo apt-get install dkms
```

### Installation of Driver
In order to install the driver open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-install.sh
```

#You may need to navigate to the kernel folder if there is a build error- 
cd /usr/src/linux-headers-4.9.140-tegra-ubuntu18.04_aarch64/kernel-4.9/arch 
#create the aarch64 folder : 
sudo mkdir aarch64
#copy the contents of the arm64 folder to this
sudo cp ./arm64/*.* ./aarch64 -R

#now remake 
sudo ./dkms-install.sh




### Removal of Driver
In order to remove the driver from your system open a terminal in the directory with the source code and execute the following command:
```
sudo ./dkms-remove.sh
```

### Make
For building & installing the driver with 'make' use
```
make
make install
```

### Notes
Download
```
git clone -b v5.3.4 https://github.com/aircrack-ng/rtl8812au.git
cd rtl*
```
Package / Build dependencies (Kali)
```
sudo apt-get install build-essential
sudo apt-get install bc
sudo apt-get install libelf-dev
sudo apt-get install linux-headers-`uname -r`
```
#### For Raspberry (RPI)

```
sudo apt-get install bc raspberrypi-kernel-headers
```

Then run this step to change platform in Makefile, For RPI 2/3:
```
$ sed -i 's/CONFIG_PLATFORM_I386_PC = y/CONFIG_PLATFORM_I386_PC = n/g' Makefile
$ sed -i 's/CONFIG_PLATFORM_ARM_RPI = n/CONFIG_PLATFORM_ARM_RPI = y/g' Makefile
```

But for RPI 3 B+ you will need to run those below which builds the ARM64 arch driver:
```
$ sed -i 's/CONFIG_PLATFORM_I386_PC = y/CONFIG_PLATFORM_I386_PC = n/g' Makefile
$ sed -i 's/CONFIG_PLATFORM_ARM64_RPI = n/CONFIG_PLATFORM_ARM64_RPI = y/g' Makefile
```

For setting monitor mode
  1. Fix problematic interference in monitor mode. 
  ```
  airmon-ng check kill
  ```
  You may also uncheck the box "Automatically connect to this network when it is avaiable" in nm-connection-editor. This only works if you have a saved wifi connection.
  
  2. Set interface down
  ```
  sudo ip link set wlan0 down
  ``` 
  3. Set monitor mode
  ```
  sudo iw dev wlan0 set type monitor
  ```
  4. Set interface up
  ```
  sudo ip link set wlan0 up
  ```
For setting TX power
```
sudo iw wlan0 set txpower fixed 3000
```

### LED control

#### You can now control LED behaviour statically by Makefile, for example:

```sh
CONFIG_LED_ENABLE = n
```
value can be y or n

#### statically by module parameter in /etc/modprobe.d/8812au.conf or wherever, for example:

```sh
options 88XXau rtw_led_enable=0
```
value can be 0 or 1

#### or dynamically by writing to /proc/net/rtl8812au/$(your interface name)/led_enable, for example:

```sh
$ echo "0" > /proc/net/rtl8812au/$(your interface name)/led_enable
```
value can be 0 or 1

#### check current value:

```sh
$ cat /proc/net/rtl8812au/$(your interface name)/led_enable
```

### NetworkManager

Newer versions of NetworkManager switches to random MAC address. Some users would prefer to use a fixed address. 
Simply add these lines below
```
[device]
wifi.scan-rand-mac-address=no
```
at the end of file /etc/NetworkManager/NetworkManager.conf and restart NetworkManager with the command:
```
sudo service NetworkManager restart
```
