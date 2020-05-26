# Raspbian Desktop Customization

**NOTE:** First install Raspbian desktop.


## Custom Image

The following instructions reference a custom image called `custom-image.png`, this can be any image.

For example, to create a black image from scratch, on the Raspberry Pi:

```
sudo apt-get install -y imagemagick
convert -size 800x600 xc:black custom-image.png
```

Alternately, to download a custom image using `wget`:

```
wget https://downloads.gordonturner.com/lord-happy-cat-max-circle.png -O custom-image.png
```


## Disable the Raspberry Icons

This will disable the raspberry core icons that appear at boot, edit `cmdline.txt`:

```
sudo vi /boot/cmdline.txt
```

Append ` logo.nologo` to the end of the line


## Disable the Rainbow Image

This will disable the rainbow image that appears on start up, edit `config.txt`:

```
sudo vi /boot/config.txt
```

Add the following:

```
# ADDED
disable_splash=1
```

## Disable "Welcome to Raspberry Pi" Application

This will prevent the "Welcome to Raspberry Pi" application from starting on first boot, delete `piwiz.desktop`:

```
sudo rm /etc/xdg/autostart/piwiz.desktop
```

## Custom GUI Splash Image

This will replace the GUI splash image with custom image.

Download your custom image (see start of document).

Move the custom image and replace the existing `splash.png`:

```
sudo mv custom-image.png /usr/share/plymouth/themes/pix/splash.png
```


## Custom Desktop Wallpaper

This will replace the wallpaper image with custom image.

Download your custom image (see start of document).

This will reuse the GUI splash image at `/usr/share/plymouth/themes/pix/splash.png`

Create folder, if necessary:

```
mkdir -p ~/.config/pcmanfm/LXDE-pi
```

Set wallpaper to custom image:

```
vi ~/.config/pcmanfm/LXDE-pi/desktop-items-0.conf
```

```
[*]
wallpaper_mode=center
wallpaper_common=1
wallpaper=/usr/share/plymouth/themes/pix/splash.png
desktop_bg=#000000000000
desktop_fg=#e8e8e8e8e8e8
desktop_shadow=#000000000000
desktop_font=PibotoLt 12
show_wm_menu=0
sort=mtime;ascending;
show_documents=0
show_trash=0
show_mounts=0
```



