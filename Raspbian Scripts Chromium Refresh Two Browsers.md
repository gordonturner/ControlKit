# Raspbian Scripts Chromium Refresh Two Browsers

Script to send a `F5` key press to the foreground app, if this is Chromium it will refresh the page.

Requires xdotool:

```
sudo apt-get install xdotool -y
```

NOTE: Assumes second browser is at 1930,10

Create a script:

```
sudo vi /opt/scripts/chromium-refresh-two-browsers.sh
```

```
#!/bin/bash

export DISPLAY=:0.0
export XAUTHORITY=/home/pi/.Xauthority


# Screen/browser 1
/usr/bin/xdotool mousemove 10 10 click 1
/usr/bin/xdotool key F5


# Screen/browser 2
/usr/bin/xdotool mousemove 1930 10 click 1
/usr/bin/xdotool key F5
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/chromium-refresh-two-browsers.sh
sudo chmod u+x /opt/scripts/chromium-refresh-two-browsers.sh
sudo chmod +x /opt/scripts/chromium-refresh-two-browsers.sh
```

Test:

```
/opt/scripts/chromium-refresh-two-browsers.sh
```


