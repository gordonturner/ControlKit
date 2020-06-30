# Raspbian Scripts Shutdown

The script content could be anything, in this example the Raspberry Pi will be shutdown.

Create a folder to store the scripts in `/opt/scripts` and create a file called `remote-shutdown.sh`:

```
sudo mkdir /opt/scripts
sudo vi /opt/scripts/remote-shutdown.sh
```

```
#!/bin/bash
sudo /sbin/shutdown now
```

```
sudo mkdir /opt/scripts
sudo vi /opt/scripts/remote-shutdown.sh
```

Change ownership and make executable:

```
sudo chown pi.pi /opt/scripts/remote-shutdown.sh
sudo chmod u+x /opt/scripts/remote-shutdown.sh
sudo chmod +x /opt/scripts/remote-shutdown.sh
```

Add the user `nobody` to the list of users who can reboot without a password:

```
sudo visudo
```

```
# ADDED
nobody ALL =NOPASSWD: /sbin/shutdown*
```

Test script, running this should shutdown the Raspberry Pi:

```
/opt/scripts/remote-shutdown.sh
```

