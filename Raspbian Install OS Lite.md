# Raspbian Install OS Lite

Follow the following instructions:  
https://www.raspberrypi.org/documentation/installation/installing-images/README.md  

Flash the image:  
`2020-02-13-raspbian-buster-lite.zip`  

Enable ssh:  

```
touch /Volumes/boot/ssh
```

Setup wifi:  

```
vi /Volumes/boot/wpa_supplicant.conf
```

```
country=CA
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

# BEGIN CHANGED
network={
        ssid="apt211"
        scan_ssid=1
        psk="XXXX"
        mode=0
        proto=WPA2
        key_mgmt=WPA-PSK
        pairwise=CCMP
        group=CCMP
        auth_alg=OPEN
        id_str="raspi"
        priority=1
}
# END CHANGED
```


## Connect to Pi

At this point during the boot an IP address will be assigned and should be printed out at startup.

If available, connect a keyboard and mouse and run the following commands.

Alternately, lookup the IP address and connect over ssh.


## Update and Configure

Login and set password:

```
passwd
```

Run:

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get autoremove -y
```

Reboot

```
sudo reboot
```

Set localization

```
sudo raspi-config
```

Set the default localization to `en_US.UTF-8 UTF-8`  

**Rational:** Have had issues where there was no localized option for en_GB.  Probably just a `Cargo Cult` change, but I still make this change

- Select `Localisation Options`  
- Select `Change Locale`  
- Unselect `en_GB.UTF-8 UTF-8`  
- Select `en_US.UTF-8 UTF-8`   
- Select `Ok`
- Set the default for the locale to `en_US.UTF-8`
- Select `Ok`

**Customize:** Use your timezone.

Set the timezone:  

- Select `Localisation Options`  
- Select `Change Timezone` 
- Select `America`, select `Ok`
- Select `Toronto`, select `Ok`

- Select `Finish`

**Customize:** Replace `control-kit-1` with the name of the system.

Update hostname to `control-kit-1`:

```
sudo vi /etc/hostname
```

```
control-kit-1
```

Reboot

```
sudo reboot
```

