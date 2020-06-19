# Raspbian Scripts Chromium Switch Tab

Script to send a Control Shift Tab key press combination to the foreground app, if this is Chromium it will switch tabs.

NOTE: It may be necessary to send an Alt Tab key press to bring the desired app or app window to foreground.

Create a script:

```
sudo vi /opt/scripts/chromium-switch-tab.sh
```

```
#!/bin/bash

export DISPLAY=:0.0
export XAUTHORITY=/home/pi/.Xauthority
/usr/bin/xdotool key Ctrl+Shift+Tab
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/chromium-switch-tab.sh
sudo chmod u+x /opt/scripts/chromium-switch-tab.sh
sudo chmod +x /opt/scripts/chromium-switch-tab.sh
```

Test:

```
/opt/scripts/chromium-switch-tab.sh
```





