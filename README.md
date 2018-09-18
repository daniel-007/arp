# arp golang

The package implements a user level arp table management in golang that
monitor the local network for ARP changes and provide notifications
when a MAC switch between online and offline.

## Force IP address change (IP spoofing)
The most useful function is to force an IP address change by claiming
the IP of a target MAC. It achieves this by persistently claiming the 
IP address using ARP request/reply and activelly hunting the target MAC
until it gives up its IP. This is very effective against mobile devices 
using DHCP however it does not work when client is using static address. 


The package uses low level arp packets to enable:
* network discovery by polling 254 addresses 
* notification when a MAC switch between online and offline
* forced IP address change 

See the example file for how to use it.

Limitations
-----------
* Tested on linux (Raspberry PI arm). Should work on Windows with tiny changes.
* IPv4 only


Getting started
---------------


```golang
	HomeRouterIP := net.ParseIP("192.168.0.1").To4()
	HomeLAN := net.IPNet{IP: net.ParseIP("192.168.0.0").To4(), Mask: net.CIDRMask(25, 32)}

	c, err := arp.NewHandler(NIC, HostMAC, HostIP, HomeRouterIP, HomeLAN)
	if err != nil {
		log.Fatal("error ", err)
	}

	go c.ListenAndServe(time.Second * 30 * 5)

	c.PrintTable()
```


To force an IP change simply invoke ForceIPChange with the current mac and ip value.
```golang
	entry := c.FindMAC("xx:xx:xx:xx:xx:xx")
	c.ForceIPChange(entry.MAC, entry.IP)
```
