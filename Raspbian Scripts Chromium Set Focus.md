# Raspbian Scripts Chromium Set Focus

Often scripts and button presses require the target app or browser to have foreground focus.  With a single screen and single fullscreen Chromium browser instance, the default focus is the Chromium instance.

However, with two screens and two full screen Chromium browser instances, focus can be more subtle.

This will click the mouse at two different locations, one for the first browser and the other for the second browser.

In this example, clicking at 10,10 will be within the first browser window and clicking 1930,10 will be within the second browser window.

Create a script:

```
sudo vi /opt/scripts/chromium-set-focus-browser1.sh
```

```
#!/bin/bash

# Screen/browser 1
export DISPLAY=:0.0
/usr/bin/xdotool mousemove 10 10
/usr/bin/xdotool mouseclick
```

Create a script:

```
sudo vi /opt/scripts/chromium-set-focus-browser2.sh
```

```
#!/bin/bash

# Screen/browser 2
export DISPLAY=:0.0
/usr/bin/xdotool mousemove 1930 10
/usr/bin/xdotool mouseclick
```



Update the permissions

```
sudo chown pi.pi /opt/scripts/chromium-set-focus-browser1.sh
sudo chown pi.pi /opt/scripts/chromium-set-focus-browser2.sh
sudo chmod u+x /opt/scripts/chromium-set-focus-browser1.sh
sudo chmod u+x /opt/scripts/chromium-set-focus-browser2.sh
sudo chmod +x /opt/scripts/chromium-set-focus-browser1.sh
sudo chmod +x /opt/scripts/chromium-set-focus-browser2.sh
```

Test:

```
/opt/scripts/chromium-set-focus-browser1.sh
/opt/scripts/chromium-set-focus-browser2.sh
```