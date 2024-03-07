# mochas

Domoticz **Moch**ad bridge **as**sistant for dim/bright RF packets

 A Python3 script to handle X10 bright/dim RF packets for dimmable devices in Domoticz

**Version 0.4 (2024-03-06)**

---

## Raison d'être

All X10 power line packet received by a CM15A controller and X10 wireless packets received by a CM15A or CM19A controller are retransmitted through a TCP socket connections by `mochad`. The Domoticz *Mochad CM15Pro/CM19A bridge with LAN interface* decodes only the On and Off packets it receives rom `mochad`. The Mochad bridge cannot decode Dim and Bright packets.

    Error: Mochad: Cannot decode 'Rx RF House: J Func: Bright' 

This script holds a dictionary of X10 units numbers and corresponding Domoticiz idx numbers of dimmable devices. It also keeps track of the last used X10 unit number. When a dim/bright packet is received, it attempts to decrease/increase the level of the last used X10 unit. It does this by obtaining the current light level of the corresponding device using the Domoticz HTTP/JSON API and then sets the new modified light level using the same API.

## Status

This version works and is already in use but it has limitations which include:

  - HTTP requests only 
  - IPv4 host addresses only
  - Username:password requests not supported. 
      - `Domoticz` and `mochas` must be on the same subnet which must be included in the **Trusted Networks (no username/password)** in the `Domoticz Security`` settings.

It has been tested with a CM15A controller which means only `mochas` has only forwarded RF packets. Assuming that there is only one difference between RF and PL packets is that two-letter label     

    02/01 18:57:11 Rx RF HouseUnit: J1 Func: On
    02/01 18:57:15 Rx PL HouseUnit: J3 Func: Off

then this helper service should work with power line packets received with the CM15A controller.

## Source

- `mochas` - the executable Python3 script that should be installed in `/usr/local/bin/`.
- `mochas.json.template` - a model JSON configuration file that should be modified and installed in `/etc/mochas/` as `mochas.json`.
- `mochad.service` - the systemd service file that could be installed in `/etc/systemd/system/`.

If `mochas` is installed in a directory other than the one suggested, `ExecStart` in `mochad.service` must be modified accordingly.

Only `root`, the owner of `mochas` in `/usr/local/bin`, should have read, write, and execute permissions over the script:   
`# sudo chmod 700 mochas`

## Configuration

The configuration file is JSON formatted. Here is the template which will have to be edited.

```json
{
    "LOGLEVEL": "error",
    "HOST": "192.168.168.168",
    "PORT": 1099,
    "HOUSE": "J",
    "DELTA": 15,
    "DOMOTICZ": "192.168.168.168:8080",
    "DEVICES": {
        "6": 66,
        "7": 177,
        "8": 288
    }
}
```

| Key | Value |
| --- | ---   |
| LOGLEVEL  | syslog level, one of : "error", "info" or "debug" |
| HOST      | mochad host IP address |
| PORT      | mochad TCP port, this is hard-coded in the mochad source so unlikely to change |
| HOUSE     | Monitored X10 house code, a letter from 'A' to 'P' |
| DELTA     | Absolute value of the change in the light level to be applied when a Dim or Bright packet is received. The light level is an integer from 0 to 100, so the delta value should be considerably less than 50 and greater than 0 of course. |
| DOMOTICZ  | Domoticz IP address and TCP port |
| DEVICES   | Map of key:value pairs where the key is a X10 unit number (from "1" to "16") and its value is the Domoticz light sensor idx number of the corresponding dimmable light |

**Note: All the keys are mandatory.** 
  - Their presence is verified when the configuration file is loaded. That is the only validity check done when reading the configuration file. 
