# mochas
Domoticz **moch**ad gateway **as**sistant for dim/bright RF packets

 A Python3 script to handle X10 bright/dim RF packets for dimmable devices in Domoticz

**Version 1.0 (2024-02-24)**

---

## Raison d'être

While `mochad` converts X10 dim/bright RF packets and transmits them through a TCP socket, the Domoticz *Mochad CM15Pro/CM19A bridge with LAN interface*  cannot decode them.

    Error: Mochad: Cannot decode 'Rx RF House: J Func: Bright' 

This script keeps track of the last used X10 unit and then attempts to decrease/increase the level of dimmable light of that unit using the Domoticz HTTP/JSON API when a dim/bright RF packet is received.

## Status

This is a first version, but it works and is already in use. 

Current limitations include:

  - HTTP requests only 
  - IPv4 host addresses only
  - User:password not supported

## Source

- `mochas` - the executable Python3 script that should be installed in `/usr/local/bin/`.
- `mochas.json` - the JSON configuration file that should be installed in `/etc/mochas/`.
- `mochad.service` - the systemd service file that could be installed in `/etc/systemd/system/`.

To create `mochas.json`, use `mochas.json.template`. A terse description of each parameter in the file is given at the start of `mochas`. Changes saved in `mochas.json` should also be made in the default values in `mochas`.

If `mochas` is installed in any other directory than one suggested, modify `ExecStart` in `mochad.service` accordingly.

Only `root`, the owner of `mochas`, should have read, write, and execute permissions:   
`# sudo chmod 700 mochas`
