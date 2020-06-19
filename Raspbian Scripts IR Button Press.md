# Raspbian Scripts IR Button Press

Create a script:

```
sudo vi /opt/scripts/press-JVC-RM-AJ777-KEY_POWER.sh
```

```
#!/bin/bash
/usr/bin/ir-ctl -d /dev/lirc0 --send=/opt/remote-config/JVC-RM-AJ777-KEY_POWER.txt
```

Update the permissions

```
sudo chown pi.pi /opt/scripts/press-JVC-RM-AJ777-KEY_POWER.sh 
sudo chmod u+x /opt/scripts/press-JVC-RM-AJ777-KEY_POWER.sh 
sudo chmod +x /opt/scripts/press-JVC-RM-AJ777-KEY_POWER.sh 
```

Test:

```
/opt/scripts/press-JVC-RM-AJ777-KEY_POWER.sh 
```


```
sudo usermod -a -G video nobody
```


```
sudo chmod a+rw /dev/lirc0
sudo chmod g+rw /dev/lirc1
```