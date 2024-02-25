# mochas
Domoticz **moch**ad gateway **as**sistant for dim/bright RF packets

 A Python3 script to handle X10 bright/dim RF packets for dimmable devices in Domoticz

**Version 1.0 (2024-02-24)**

---

## Raison d'être

While `mochad` converts X10 dim/bright RF packets and transmits them through a TCP socket, the Domoticz *Mochad CM15Pro/CM19A bridge with LAN interface*  cannot decode them.

    Error: Mochad: Cannot decode 'Rx RF House: J Func: Bright' 

This script holds a list of X10 units numbers and corresponding Domoticiz idx numbers for dimmable lights. It also keeps track of the last used X10 unit number. When a dim/bright packet is received, it attempts to decrease/increase the level of the last used X10 unit. It does this by obtaining the current light level of the corresponding device using the Domoticz HTTP/JSON API and then sets the new decreased or increased light level using the same API.

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
