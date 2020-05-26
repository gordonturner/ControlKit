# Raspbian GPIO IR Hardware

# Infrared Remote Hardware Circuit Receiver

## Receiver Wiring Diagram

A) White wire, Pin 1, +3V3,    output power  
B) Green wire, Pin 6, GND,     ground  
C) Blue wire,  Pin 8, GPIO 14, input signal  

```
              Pin 1 Pin2
           +3V3 [A] [ ] +5V
 SDA1 / GPIO  2 [ ] [ ] +5V
 SCL1 / GPIO  3 [ ] [B] GND
        GPIO  4 [ ] [ ] GPIO 14 / TXD0
            GND [ ] [ ] GPIO 15 / RXD0
        GPIO 17 [ ] [ ] GPIO 18
        GPIO 27 [ ] [C] GND
        GPIO 22 [ ] [ ] GPIO 23
           +3V3 [ ] [ ] GPIO 24
 MOSI / GPIO 10 [ ] [ ] GND
 MISO / GPIO  9 [ ] [ ] GPIO 25
 SCLK / GPIO 11 [ ] [ ] GPIO  8 / CE0#
            GND [ ] [ ] GPIO  7 / CE1#
             Pin 25 Pin 26
```

Setup the sensor on the breadboard. There are three leads, if you are using the TSOP38238, lens (‘bump’ on sensor) facing up and should be connected as follows:

```
+-----------------------+ A
|                       +-----------o +3.3V, Pin 1
|                       |						
|      ______________   |
|     /                 |
|    (                  | B  
|     \______________ + +-----------o GND, Pin 6
|                       |
|                       | C
|                     - +-----------o GPIO 14, Pin 8
+-----------------------+
```

Where to buy TSOP38238 infrared receiver:  
https://www.creatroninc.com/product/tsop38238-infrared-receiver-38khz/  
https://www.sparkfun.com/products/10266  
https://www.adafruit.com/product/157  

Consider buying 7 pin housing to make it easier to connect all the wires at once:   
https://www.creatroninc.com/product/7-pin-jr-f-connector-set/  


# Infrared Remote Hardware Circuit Transmitter

## Transmitter Wiring Diagram

A) Black wire,  Pin 2,  +5V,     output power  
B) Orange wire, Pin 10, GPIO 15, control signal  
C) Green wire,  Pin 14, GND,     ground  

```
              Pin 1 Pin2
           +3V3 [ ] [A] +5V
 SDA1 / GPIO  2 [ ] [ ] +5V
 SCL1 / GPIO  3 [ ] [ ] GND
        GPIO  4 [ ] [ ] GPIO 14 / TXD0
            GND [ ] [B] GPIO 15 / RXD0
        GPIO 17 [ ] [ ] GPIO 18
        GPIO 27 [ ] [C] GND
        GPIO 22 [ ] [ ] GPIO 23
           +3V3 [ ] [ ] GPIO 24
 MOSI / GPIO 10 [ ] [ ] GND
 MISO / GPIO  9 [ ] [ ] GPIO 25
 SCLK / GPIO 11 [ ] [ ] GPIO  8 / CE0#
            GND [ ] [ ] GPIO  7 / CE1#
             Pin 25 Pin 26
```

Components:  

```
     Pin 2 A o----------------------------+
     (+5V)                                |   (longer lead towards pin 2)
                                         _|_
                                         \ /  --> LTE-4208
                                         ---  --> IR LED
                                          |
                                BC547     |
                   ___                +---+  [Emitter]
    Pin 10 B o----|___|---------------|
    (GPIO15)     220 ohm       [Base] +->-+  [Collector]
                                          |
                                          |
    Pin 14 C o----------------------------+                                    
    (GND)
```  

Consider buying 7 pin housing to make it easier to connect all the wires at once:   
https://www.creatroninc.com/product/7-pin-jr-f-connector-set/  


## Pre Wired IR Blasters Mono Jack

Consider buying prewired emitters / IR Blasters, cheaper and easier to connect if used with the mono jack.

Cable, single emitter:  
https://www.amazon.ca/Infrared-Emitter-Extender-Extension-Transmitter/dp/B01H5A3IWY/ref=pd_bxgy_img_3/140-8239435-3587563

Cable, triple emitter:  
https://www.amazon.ca/Infrared-Emitter-Extender-Extension-Transmitter/dp/B01NACWAG4/

Cable, plug emitter:  
https://www.amazon.ca/Infrared-Universal-Control-Conditioner-Silvery/dp/B07C4Y2VFV/


## Mono Jack

If using the prewired emitters / IR Blasters, also consider getting a mono jack to connect to.

3.5mm mono jack panel mount:  
https://www.creatroninc.com/product/35mm-mono-jack-panel-mount/
https://www.amazon.com/Nine-3-5mm-Stereo-Panel-Connector/dp/B01C3RFHDC
https://www.amazon.com/Tegg-PJ-307-Stereo-Headphone-Connector/dp/B07MFKKWG5

**3 connectors**  
- Orientation, female connection pointing away, flat centre contact at bottom  
- Left solder point, is centre contact   
- Right solder point, is outer contacts  

**NOTE:** Left solder point is the connection to the Raspberry Pi GPIO  
- The longer wire in the actual LED, the anode  
- Blue wire in the prototype  

**NOTE:** Right solder point is the connection to the transistor in the circuit  


Use `irc-ctl` to test:

```
ir-ctl -d /dev/lirc0 --send=/opt/remote-config/JVC-RM-AJ777-KEY_POWER.txt
```








