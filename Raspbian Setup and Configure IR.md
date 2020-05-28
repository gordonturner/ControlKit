# Raspbian Setup and Configure IR

Setup a Raspberry Pi GPIO, using ir-ctl and ir-table with no LIRC involved.


## Reference

2020-04-14-ir-test1.md  

https://www.sigmdel.ca/michel/ha/opi/ir_03_en.html  
https://www.mess.org/2019/03/04/How-to-add-support-for-a-new-remote-control/  
http://atterer.org/mythtv-xmbc-remote-control-without-lirc  
https://www.mess.org/2020/01/26/Moving-from-lirc-tools-to-rc-core-tooling/  


## Hardware

See `Raspbian GPIO IR Hardware.md`


## Customize `config.txt`

Update the `config.txt` variables:

```
sudo vi /boot/config.txt
```

```
# BEGIN ADDED
dtoverlay=gpio-ir,gpio_pin=14
dtoverlay=gpio-ir-tx,gpio_pin=15
# END ADDED
```

Reboot

```
sudo reboot
```

Confirm gpio modules are loaded:

```
lsmod | grep gpio
```

```
gpio_ir_recv           16384  0
gpio_ir_tx             16384  0
```

```
cat /proc/bus/input/devices
```

```
I: Bus=0019 Vendor=0001 Product=0001 Version=0100
N: Name="gpio_ir_recv"
P: Phys=gpio_ir_recv/input0
S: Sysfs=/devices/platform/ir-receiver@e/rc/rc0/input0
U: Uniq=
H: Handlers=kbd event0 
B: PROP=0
B: EV=100013
B: KEY=fff 0 0 4200 108fc32e 2376051 0 0 0 7 158000 4192 4001 8e9680 0 0 10000000
B: MSC=10
```


## A Note About rc0 and rc1

Normally the ir receiver device is assigned to `/sys/class/rc/rc0`.  However, due to the nature of multi threaded device probe, the receiver device can be assigned to `/sys/class/rc/rc1`.  

This is not a bug and can happen as result of the device probing.

In the following notes, when ir-keytable is called with `-s rc0` and there is no response, use `-s r1`.

**NOTE:** If no `-s` is specified, rc0 is default.  However due to the possibility of the receiver being assigned to rc1 during boot, it is recommended to always specify `-s`.  In this way if the 'wrong' device is used and error will appear.


## Install `ir-keytable`

Install `ir-keytable`, run:

```
sudo apt-get install ir-keytable -y
```

To confirm install, run:

```
ir-keytable
```

```
Found /sys/class/rc/rc0/ (/dev/input/event0) with:
	Name: gpio_ir_recv
	Driver: gpio_ir_recv, table: rc-rc6-mce
	LIRC device: /dev/lirc1
	Attached BPF protocols: Operation not permitted
	Supported kernel protocols: lirc rc-5 rc-5-sz jvc sony nec sanyo mce_kbd rc-6 sharp xmp imon 
	Enabled kernel protocols: lirc rc-6 
	bus: 25, vendor/product: 0001:0001, version: 0x0100
	Repeat delay = 500 ms, repeat period = 125 ms
```  


## Test Remote with `ir-keytable`

Test if the remote is using an existing ir kernel protocol, enable all the kernel protocols:  

```
sudo ir-keytable -p all
```

```
Protocols changed to lirc rc-5 rc-5-sz jvc sony nec sanyo mce_kbd rc-6 sharp xmp imon 
```

**NOTE:** The `lirc` protocol will always be enabled, it's not a real protocol, it is the raw signals via `/dev/lircX`.  

Confirm the ir receiver device is working:

```
sudo ir-keytable
```

```
Found /sys/class/rc/rc0/ (/dev/input/event0) with:
	Name: gpio_ir_recv
	Driver: gpio_ir_recv, table: rc-rc6-mce
	LIRC device: /dev/lirc1
	Attached BPF protocols: Operation not supported
	Supported kernel protocols: lirc rc-5 rc-5-sz jvc sony nec sanyo mce_kbd rc-6 sharp xmp imon 
	Enabled kernel protocols: lirc rc-5 rc-5-sz jvc sony nec sanyo mce_kbd rc-6 sharp xmp imon 
	bus: 25, vendor/product: 0001:0001, version: 0x0100
	Repeat delay = 500 ms, repeat period = 125 ms
```

**NOTE:** The `/sys/class/rc/rc0/` indicates that the rc0 device is being used

Test remote with rc0:

```
ir-keytable -t -s rc0
```

Point remote at the receiver and press buttons, confirm responses to button presses, output should be similar to:

```
Testing events. Please, press CTRL-C to abort.
756.890042: lirc protocol(rc6_mce): scancode = 0x800f0416 toggle=1
756.890091: event type EV_MSC(0x04): scancode = 0x800f0416
756.890091: event type EV_KEY(0x01) key_down: KEY_PLAY(0x00cf)
756.890091: event type EV_SYN(0x00).
757.080039: event type EV_KEY(0x01) key_up: KEY_PLAY(0x00cf)
757.080039: event type EV_SYN(0x00).
757.110056: lirc protocol(rc6_mce): scancode = 0x800f0416
757.110092: event type EV_MSC(0x04): scancode = 0x800f0416
757.110092: event type EV_KEY(0x01) key_down: KEY_PLAY(0x00cf)
757.110092: event type EV_SYN(0x00).
757.300037: event type EV_KEY(0x01) key_up: KEY_PLAY(0x00cf)
757.300037: event type EV_SYN(0x00).
757.310052: lirc protocol(rc6_mce): scancode = 0x800f0416 toggle=1
757.310082: event type EV_MSC(0x04): scancode = 0x800f0416
757.310082: event type EV_KEY(0x01) key_down: KEY_PLAY(0x00cf)
757.310082: event type EV_SYN(0x00).
757.500038: event type EV_KEY(0x01) key_up: KEY_PLAY(0x00cf)
757.500038: event type EV_SYN(0x00).
```

**NOTE:** The `rc6_mce` indicates that the remote is using the rc6_mce protocol.  

**NOTE:** If the protocol is not identified, the remote is not using a recognized kernel protocol and additional configuration is needed.


## 2020-02-13-raspbian-buster udev Bug

There is a bug in the udev rules for 2020-02-13-raspbian-buster that will prevent the rc_keymaps from being loaded at start.

To fix, edit:

```
sudo vi /lib/udev/rules.d/60-ir-keytable.rules
```

```
# CHANGED
#ACTION=="add", SUBSYSTEM=="rc", RUN+="/usr/bin/ir-keytable -a /etc/rc_maps.cfg -s $name"
ACTION=="add", SUBSYSTEM=="input", SUBSYSTEMS=="rc", KERNEL=="event*", ENV{.rc_sysdev}="$id", RUN+="/usr/bin/ir-keytable -a /etc/rc_maps.cfg -s $env{.rc_sysdev}"
```

## Configure `ir-keytable` for Kernel Protocol

Copy the configuration from `/lib/udev/rc_keymaps` for the remote to `/etc/rc_keymaps`.

Example, if the MCE remote supports the common `rc-6` keymap:

```
sudo cp /lib/udev/rc_keymaps/rc6_mce.toml /etc/rc_keymaps
```

Reboot:

```
sudo reboot
```

Test with:

```
ir-keytable -t -s rc0
```

Output should be:

```
Testing events. Please, press CTRL-C to abort.
1001.030050: lirc protocol(rc6_mce): scancode = 0x800f0416
1001.030096: event type EV_MSC(0x04): scancode = 0x800f0416
1001.030096: event type EV_KEY(0x01) key_down: KEY_PLAY(0x00cf)
1001.030096: event type EV_SYN(0x00).
1001.220039: event type EV_KEY(0x01) key_up: KEY_PLAY(0x00cf)
1001.220039: event type EV_SYN(0x00).
1001.270088: lirc protocol(rc6_mce): scancode = 0x800f0416 toggle=1
1001.270120: event type EV_MSC(0x04): scancode = 0x800f0416
1001.270120: event type EV_KEY(0x01) key_down: KEY_PLAY(0x00cf)
1001.270120: event type EV_SYN(0x00).
1001.460032: event type EV_KEY(0x01) key_up: KEY_PLAY(0x00cf)
1001.460032: event type EV_SYN(0x00).
```



## Transmit Remote Key Presses for Remotes with Supported Protocols

First, determine the keycode from the keymap file.

Example, send the KEY_SLEEP scancode for the MCE remote rc-6.

Find the scan code by searching for 'SLEEP':

```
cat /etc/rc_keymaps/rc6_mce.toml | grep SLEEP
```

```
0x800f040c = "KEY_SLEEP"
```

Using the `rc6_mce` protocol and `0x800f040c` scancode, run:

```
ir-ctl -S rc6_mce:0x800f040c -d /dev/lirc1
```

**NOTE:** Although `/dev/lirc1` is used, lirc is not installed.


## Transmit Remote Key Presses for Remotes with Unsupported Protocols

There are some remote IR protocols are not supported by the kernel.  For those remotes, it is possible to capture and transmit the raw output.

For each button press, redirect the input to a file.

For example, capture the power button by redirecting the input to `JVC-RM-AJ777-KEY_POWER.txt`:

```  
ir-ctl -d /dev/lirc1 -r > /opt/remote-config/JVC-RM-AJ777-KEY_POWER.txt
```  

To transmit the captured codes, run ir-ctl using the `JVC-RM-AJ777-KEY_POWER.txt` file as input:

```
ir-ctl -d /dev/lirc0 --send=/opt/stupidremote-remote-config/JVC-RM-AJ777-KEY_POWER.txt
```  
