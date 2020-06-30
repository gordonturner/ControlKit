# Raspbian Scripts Reboot

The script content could be anything, in this example the Raspberry Pi will be rebooted.

Create a folder to store the scripts in `/opt/scripts` and create a file called `remote-reboot.sh`:

```
sudo mkdir /opt/scripts
sudo vi /opt/scripts/remote-reboot.sh
```

```
#!/bin/bash
sudo /sbin/shutdown --reboot now
```

Change ownership and make executable:

```
sudo chown pi.pi /opt/scripts/remote-reboot.sh
sudo chmod u+x /opt/scripts/remote-reboot.sh
sudo chmod +x /opt/scripts/remote-reboot.sh
```

Add the user `nobody` to the list of users who can reboot without a password:

```
sudo visudo
```

```
# ADDED
nobody ALL =NOPASSWD: /sbin/shutdown*
```

Test script, running this should restart the Raspberry Pi:

```
/opt/scripts/remote-reboot.sh
```

