# Disable Wifi & bluetooth

Edit the raspberry pi boot config

```sh
sudo nano /boot/firmware/config.txt
```

Add the following lines (Raspberry pi 5 specific) under the `[all]`

```properties
dtoverlay=disable-wifi-pi5
dtoverlay=disable-bt-pi5
```

# Configure via NetworkManager


List currently active connections

```sh
sudo nmcli -p connection show
```


Delete them, based on UUID or name

```sh

sudo nmcli connection delete [UUID]
sudo nmcli connection delete netplan-eth0
```

Confirm the connection configuration was deleted

```sh
sudo nmcli -p connection show
```

# Set up the 2 wired connections (uplink & local)

## Uplink

```sh
sudo nmcli connection add type ethernet con-name eth2 ifname eth2 ipv4.method auto
sudo nmcli connection up eth2
```

## Local network

Create bridge interface

Disable STP (Spanning Tree Protocol):

STP exists to shut down bridge ports if a bridging loop is created by mistake. At least in the typical use case for this configuration - bridging virtual machines to one of your host NICs - it is virtually impossible to accidentally create a loop. Further, leaving it enabled will cause your physical NIC to broadcast STP packets to the rest of the network and some datacentre and enterprise networks will consider STP packets on an access port to be malicious and shut down the port.

In most scenarios leaving it enabled is harmless, but personally I would almost always disable it unless I was doing something funky enough that an accidental bridging loop was plausible.

This prevents a 30-second delay when bringing the interface up.

```sh
sudo nmcli connection add type bridge con-name br0 ifname br0
sudo nmcli connection add type bridge-slave ifname eth1 master br0
sudo nmcli connection modify br0 bridge.stp no
sudo nmcli connection modify br0 ipv4.addresses 10.0.0.1/24
sudo nmcli connection modify br0 ipv4.method manual
sudo nmcli connection up br0
```


## Check connection status

```sh
nmcli dev status
nmcli connection show
ifconfig
```


## Add bridge