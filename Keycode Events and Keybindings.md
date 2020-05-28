# Keycode Events and Keybindings

**Keycode** Generally, a keycode is a low level event input typically received from a mouse or keyboard.  

**Keybinding** A keybinding is a way of triggering something when a key or combination of keys are pressed.  Eg control alt delete is a keybinding to reboot the computer.  

## Keycode Events

**Assumption:** The remote control is configured to send keycode events similar to a keyboard or mouse.

To monitor keycode events, run:

```
export DISPLAY=:0.0
sudo showkey
```

Point remote at the receiver, press buttons and confirm responses to button presses.

The output should look like:

```  
kb mode was UNICODE
[ if you are trying this under X, it might not work
since the X server is also reading /dev/console ]

press any key (program terminates 10s after last keypress)...
keycode   1 release
keycode  79 release
keycode 207 press
keycode   1 release
keycode  79 release
keycode 207 press
keycode 207 press
keycode 207 press
```  


## Headless or No Desktop Keybindings

Use this approach if the system is headless, has no monitor and not using a desktop.

**NOTE:** The following assumes a script `remote-poweroff.sh` exists and is working (see "Shutdown Script" at the end of this document).

This uses a service called **triggerhappy**, it can map events to scripts and is installed by default on Raspbian.

To view the input that the tiggerhappy  is receiving, start triggerhappy in dump mode:

```
thd --dump /dev/input/event*
```

Press a key or button on the remote to confirm it is being received, the output should look like:

```
EV_KEY	KEY_SLEEP	1	/dev/input/event0
# KEY_SLEEP	1	command
EV_KEY	KEY_SLEEP	0	/dev/input/event0
# KEY_SLEEP	0	command
EV_KEY	KEY_SLEEP	1	/dev/input/event0
# KEY_SLEEP	1	command
EV_KEY	KEY_SLEEP	0	/dev/input/event0
# KEY_SLEEP	0	command
```

Type control-c to exit.

To map a key or button to a script, create a new configuration file:

```
sudo vi /etc/triggerhappy/triggers.d/remote-poweroff.conf
```

Add the configuration:

```
KEY_SLEEP                   1       /opt/scripts/remote-poweroff.sh
```

Restart triggerhappy to load changes:

```
sudo systemctl restart triggerhappy
```

Test by pressing the configured key or button, this should call `remote-poweroff.sh` and start a shutdown.

Manage:

```
sudo systemctl status triggerhappy
sudo systemctl restart triggerhappy
sudo systemctl start triggerhappy
sudo systemctl stop triggerhappy
```

Logs:

```
journalctl -f -u triggerhappy
```


## Desktop Openbox Keybindings

Use this approach if using the Openbox desktop.

**NOTE:** The following assumes a script `remote-poweroff.sh` exists and is working (see "Shutdown Script" at the end of this document).  

**NOTE:** The key mapping from the file configured in `/etc/rc_keymaps/` may not match the value received in the desktop.

To view the input that the desktop is receiving, on the desktop open a terminal and run:

```
xev
```


Press a key or button on the remote to confirm it is being received, the output should look like:

```
KeyRelease event, serial 45, synthetic NO, window 0x1400001,
    root 0x35e, subw 0x0, time 47220, (-530,-438), root:(22,18),
    state 0x0, keycode 150 (keysym 0x1008ff2f, XF86Sleep), same_screen YES,
    XLookupString gives 0 bytes: 
    XFilterEvent returns: False
```

Inspect the output for the `keycode` value.  In this case `KEY_SLEEP` was `XF86Sleep`, use that value to map in the `lxde-pi-rc.xml` file.

Copy and customize `lxde-pi-rc.xml`:

```
mkdir -p ~/.config/openbox/
cp /etc/xdg/openbox/lxde-pi-rc.xml ~/.config/openbox/lxde-pi-rc.xml
vi ~/.config/openbox/lxde-pi-rc.xml
```

Add just below `<keyboard>`:

```
  <!-- BEGIN Adding keybindings -->
 
  <keybind key="XF86Sleep">
    <action name="Execute">
      <command>/opt/scripts/remote-poweroff.sh</command>
    </action>
  </keybind>
  
  <!-- END Adding keybindings -->
```

Reboot and test

Reference:  
http://openbox.org/wiki/Help:Bindings


## Remote Control Scancode to Key Remap

It might be preferable to have one key map to one script.  Then all inputs trigger that one key.

It is possible to remap remote button presses and scancodes to a keyboard key by editing the `/etc/rc_keymaps` toml file

For example, remap Xbox One remote buttons to F8, F9 and F10 key presses.

Using the Xbox One remote, get the scancode for three buttons:

```
sudo ir-keytable -p all
ir-keytable -t -s rc0
```

```
Testing events. Please, press CTRL-C to abort.
1033.400048: lirc protocol(necx): scancode = 0x80d86e
1033.400071: event type EV_MSC(0x04): scancode = 0x80d86e
1033.400071: event type EV_SYN(0x00).
1033.460057: lirc protocol(necx): scancode = 0x80d86e repeat
1033.460092: event type EV_MSC(0x04): scancode = 0x80d86e
1033.460092: event type EV_SYN(0x00).
1034.100039: lirc protocol(necx): scancode = 0x80d864
1034.100064: event type EV_MSC(0x04): scancode = 0x80d864
1034.100064: event type EV_SYN(0x00).
1035.150039: lirc protocol(necx): scancode = 0x80d86f
1035.150063: event type EV_MSC(0x04): scancode = 0x80d86f
1035.150063: event type EV_SYN(0x00).
1035.200042: lirc protocol(necx): scancode = 0x80d86f repeat
1035.200065: event type EV_MSC(0x04): scancode = 0x80d86f
1035.200065: event type EV_SYN(0x00).
```

Create new toml file for the scancode to key mapping:

```
sudo vi /etc/rc_keymaps/xbox_one_remote.toml
```

```
[[protocols]]
name = "xbox_one_remote"
protocol = "nec"
variant = "necx"
[protocols.scancodes]
0x80d86e = "KEY_F8"
0x80d864 = "KEY_F9"
0x80d86f = "KEY_F10"
```

Test toml file:

```
sudo ir-keytable -c -w /etc/rc_keymaps/xbox_one_remote.toml
```

```
Testing events. Please, press CTRL-C to abort.
1153.990047: lirc protocol(necx): scancode = 0x80d86e
1153.990077: event type EV_MSC(0x04): scancode = 0x80d86e
1153.990077: event type EV_KEY(0x01) key_down: KEY_F8(0x0042)
1153.990077: event type EV_SYN(0x00).
1154.050037: lirc protocol(necx): scancode = 0x80d86e repeat
1154.050062: event type EV_MSC(0x04): scancode = 0x80d86e
1154.050062: event type EV_SYN(0x00).
1154.180032: event type EV_KEY(0x01) key_up: KEY_F8(0x0042)
1154.180032: event type EV_SYN(0x00).
1155.200039: lirc protocol(necx): scancode = 0x80d864
1155.200066: event type EV_MSC(0x04): scancode = 0x80d864
1155.200066: event type EV_KEY(0x01) key_down: KEY_F9(0x0043)
1155.200066: event type EV_SYN(0x00).
1155.250060: lirc protocol(necx): scancode = 0x80d864 repeat
1155.250081: event type EV_MSC(0x04): scancode = 0x80d864
1155.250081: event type EV_SYN(0x00).
1155.380035: event type EV_KEY(0x01) key_up: KEY_F9(0x0043)
1155.380035: event type EV_SYN(0x00).
1155.880038: lirc protocol(necx): scancode = 0x80d86f
1155.880064: event type EV_MSC(0x04): scancode = 0x80d86f
1155.880064: event type EV_KEY(0x01) key_down: KEY_F10(0x0044)
1155.880064: event type EV_SYN(0x00).
1155.930060: lirc protocol(necx): scancode = 0x80d86f repeat
1155.930081: event type EV_MSC(0x04): scancode = 0x80d86f
1155.930081: event type EV_SYN(0x00).
1156.060034: event type EV_KEY(0x01) key_up: KEY_F10(0x0044)
1156.060034: event type EV_SYN(0x00).
```

Should now see `KEY_F8`, `KEY_F9` and `KEY_F10` in the output.

Next, add the toml file to be loaded at startup:

```
sudo vi /etc/rc_maps.cfg 
```

```
# ADDED
*       *                        xbox_one_remote.toml # Custom remote
```

Finally, add the appropriate keybindings, either through openbox or triggerhappy.
