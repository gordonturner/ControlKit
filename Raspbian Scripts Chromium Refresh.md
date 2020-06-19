# Raspbian Scripts Chromium Refresh

Script to send a `F5` key press to the foreground app, if this is Chromium it will refresh the page.

NOTE: It may be necessary to send an Alt Tab key press to bring the desired app or app window to foreground.

Create a script:

```
sudo vi /opt/scripts/chromium-refresh.sh
```

```
#!/bin/bash

export DISPLAY=:0.0
export XAUTHORITY=/home/pi/.Xauthority
/usr/bin/xdotool key F5
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/chromium-refresh.sh
sudo chmod u+x /opt/scripts/chromium-refresh.sh
sudo chmod +x /opt/scripts/chromium-refresh.sh
```

Test:

```
/opt/scripts/chromium-refresh.sh
```


