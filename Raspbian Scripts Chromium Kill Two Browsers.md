# Raspbian Scripts Chromium Refresh Two Browsers

Script to call `pkill` on the oldest thread called `chromium`.

Using kill is a bit of a nuclear option, consider closing more cleanly.

```
sudo vi /opt/scripts/chromium-kill-two-browsers.sh
```

```
#!/bin/bash

export DISPLAY=:0.0

# Screen/browser 1
pkill -o chromium

sleep 2;

# Screen/browser 2
pkill -o chromium
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/chromium-kill-two-browsers.sh
sudo chmod u+x /opt/scripts/chromium-kill-two-browsers.sh
sudo chmod +x /opt/scripts/chromium-kill-two-browsers.sh
```

Test:

```
/opt/scripts/chromium-kill-two-browsers.sh
```


