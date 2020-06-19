# Raspbian Scripts Screen Control

Create a script:

```
sudo vi /opt/scripts/screen-control.sh
```

```
#!/bin/bash
#
# version 2.0
#
# Updates
#
# - Using `vcgencmd display_power` to control screens
# - Support for dual displays via `vcgencmd display_power`
# - Removed `fbset`, `xrefresh`, `xdotool` no longer needed
# 
#
# NOTE: screen-control.log will grow, consider disabling the output or delete it periodically
#

echo `date` "Called with $1" >> ~/screen-control.log

# NOTE: Using the first screen to determine STATE
STATE=`/usr/bin/vcgencmd display_power -1`

if [ ! -z "$1" ] && [ $1 = "toggle" ]; then

  echo `date` "Toggling screen."
  echo `date` "Toggling screen." >> ~/screen-control.log

  if [[ $STATE == *"display_power=0"* ]]; then

    echo `date` "Screen is off, turning on."
    echo `date` "Screen is off, turning on." >> ~/screen-control.log
    /usr/bin/vcgencmd display_power 1
    
  else

    echo `date` "Screen is on, turning off."
    echo `date` "Screen is on, turning off." >> ~/screen-control.log
    
    # Turn off all screens
    /usr/bin/vcgencmd display_power 0
    
    # Screen is off, no need to refresh
    
  fi

elif [ ! -z "$1" ] && [ $1 = "on" ]; then

  if [[ $STATE == *"display_power=0"* ]]; then

    echo `date` "Screen is off, turning on."
    echo `date` "Screen is off, turning on." >> ~/screen-control.log
    /usr/bin/vcgencmd display_power 1
    
  else

    echo `date` "Screen is on, doing nothing."
    echo `date` "Screen is on, doing nothing." >> ~/screen-control.log
    
    # Screen is off, no need to refresh
    
  fi

elif [ ! -z "$1" ] && [ $1 = "off" ]; then

  if [[ $STATE == *"display_power=0"* ]]; then

    echo `date` "Screen is off, doing nothing."
    echo `date` "Screen is off, doing nothing." >> ~/screen-control.log
    
    # Screen is off, no need to refresh
    
  else

    echo `date` "Screen is on, turning off."
    echo `date` "Screen is on, turning off." >> ~/screen-control.log
    /usr/bin/vcgencmd display_power 0
    
    # Screen is off, no need to refresh
    
  fi

else
  # Print usage
  echo
  /opt/vc/bin/tvservice -s
  echo
  echo usage:
  echo $0 on
  echo $0 off
  echo $0 toggle

fi
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/screen-control.sh
sudo chmod u+x /opt/scripts/screen-control.sh
sudo chmod +x /opt/scripts/screen-control.sh
```

Test:

```
/opt/scripts/screen-control.sh
```