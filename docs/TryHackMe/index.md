---
title: TryHackMe
layout: default
---

# OpenVPN
## Get Connected
### Download the OpenVPN GUI open-source application.
- Arch Linux
    - [OpenVPN](https://wiki.archlinux.org/title/OpenVPN)
        - [qopenvpn](https://archlinux.org/packages/?name=qopenvpn)
        - [networkmanager-openvpn](https://archlinux.org/packages/?name=networkmanager-openvpn)

### Import the VPN configuration file
- [Download](https://tryhackme.com/access)

### Connecting
- Arch Linux
    - Download, `sudo pacman -Syu openvpn`
    - Connect, `sudo openvpn /path-to-file/file-name.ovpn`