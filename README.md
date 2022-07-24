# yeelight-c900
### UART
JP3 uart
| --- |
| GND |
| Rx  |
| Tx  |
| Vcc |

### gpio for light 
test result is :
```
gpio21 --> cold
gpio19 --> warm
gpio23 --> night mode
```
so, the light gpio is same as yeelink.light.ceiling15

### gpio for fan

```
gpio16( RX )  --->  con2 ( TX )
gpio17( TX )  --->  con2 ( RX )
EN( pin3 )    --->  con2( wifi_en )
gpio33        --->  fan_buzzer_beep
```

### remote controller adv data 

the data style is `50.30.8E.06` + `frame number` + `mac address` + `01.10.03` + `key code` + `00.00`
```
# for fan
[21:53:04][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.8B.0B.85.6E.38.C1.A4.01.10.03.00.00.00 (17)
# for light
[21:52:09][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.89.0B.85.6E.38.C1.A4.01.10.03.01.00.00 (17)
# for recirculating air
[21:54:25][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.8E.0B.85.6E.38.C1.A4.01.10.03.02.00.00 (17)
# for warn
[21:56:14][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.94.0B.85.6E.38.C1.A4.01.10.03.03.00.00 (17)
# for night light
[21:54:49][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.90.0B.85.6E.38.C1.A4.01.10.03.04.00.00 (17)
# for brightness
[21:55:25][D][ble_adv:107]:     - 0xFE95: (length 17)  - 50.30.8E.06.92.0B.85.6E.38.C1.A4.01.10.03.05.00.00 (17)
```

for remove repeat adv data,  add global variable frame_counter, that store previous frame number `x[4]`. 
```
         - if:
            condition: 
              lambda: "return(x[14] == 1 && id(frame_counter) != x[4]);"
            then:
             - lambda: |-
                 id(frame_counter) = x[4];

```

### sniff communication between esp32 and BP6601 using uart

```
# on
[D][uart_debug:114]: >>> 01:04:01:18:13:03 -> 0x04 + 0x01 + 0x13 = 0x18
[D][uart_debug:114]: <<< 01:F3:01:07:13:03 -> 0xF3 + 0x01 + 0x13 = 0x07

# off
[D][uart_debug:114]: >>> 01:01:01:13:11:03 -> 0x01 + 0x01 + 0x11 = 0x13
[D][uart_debug:114]: <<< 01:F3:01:05:11:03 -> 0xF3 + 0x01 + 0x11 = 0x05

# 1 level
[D][uart_debug:114]: >>> 01:03:01:05:01:03 -> 0x03 + 0x01 + 0x01 = 0x05
[D][uart_debug:114]: <<< 01:F3:01:F5:01:03 -> 0xF3 + 0x01 + 0x01 = 0xF5

# 2 level
[D][uart_debug:114]: >>> 01:03:01:36:32:03 -> 0x03 + 0x01 + 0x32 = 0x36
[D][uart_debug:114]: <<< 01:F3:01:26:32:03 -> 0xF3 + 0x01 + 0x32 = 0x26

# 3 level 
[D][uart_debug:114]: >>> 01:03:01:68:64:03 -> 0x03 + 0x01 + 0x64 = 0x68
[D][uart_debug:114]: <<< 01:F3:01:58:64:03 -> 0xF3 + 0x01 + 0x64 = 0x58


Byte  Value  Description
0     0x01   Start of frame (always 0x01)
1     0x03   0x01=Off, 0x03=On, 0x04=OnReverse
2     0x01   ??
3     0x68   Checksum (byte1 + byte2 + byte4)
4     0x64   Fan speed 0x01...0x64 = 1...100%
5     0x03   End of frame (always 0x03)
```

### Credits, thanks for help
[yeelight_fan_controller](https://github.com/syssi/esphome-yeelight-ceiling-light/tree/main/components/yeelight_fan_controller) @syssi

### References, sources & inspiration
https://github.com/syssi/esphome-yeelight-ceiling-light

https://github.com/arendst/Tasmota/discussions/13664#discussioncomment-2929489

### license MIT
