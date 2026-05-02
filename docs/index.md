---
title: Project
toc: true
---

This guide describes how to configure a Raspberry Pi 5 as a home server. It can also probably be used for earlier Raspberry Pi devices, or even regular computers.

## Goals

My goal with this project was to have a single tiny yet mighty device, and associated storage, that fits nicely within an electricity cabinet, and _literally_ does it all.

It needs to provide the following features for the entire home network:
- Firewall/Router acting as gateway
- DHCP/DNS server
- Network attached storage
- Remote Time Machine backup target
- Network-wide ad-blocker
- Private Home Assistant instance runner
- SMTP mail relay server
- ...

Networking considerations aside, it should also ensure that:
- All data and credentials are secure, and all disks are encrypted.
- Backups are performed automatically ... or rather as automatically as possible.

## Why make it available publicly?

I originally created it as a future reference for myself. I later realized it might be of interest to others, because such a consolidated reference for a setup like this one does not seem to exist elsewhere.

## Disclaimer

This guide is provided without warranties, and I cannot be held responsible for any problems that may arise from using it. You should always know and understand what you are doing.

## Report an issue

If you see something wrong in this guide, or you have a suggestion on how to improve it, I would love to hear about it! I tried to make issue reporting as easy as possible. [Check out how to report issues over here](./contribute/report-issue.html).

## Support me

If you would like to offer your appreciation for my expertise and efforts with this guide, you can [buy me a coffee](https://www.buymeacoffee.com/ericfjosne)!

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Support-yellow?style=for-the-badge&logo=buy-me-a-coffee)](https://www.buymeacoffee.com/ericfjosne)