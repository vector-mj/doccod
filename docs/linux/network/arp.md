---
comments: true
password: 1123
---

# ARP


  ![Image title](../../assets/background.png)

## What is ARP ?
ARP or __Address Resolution Protocol__ is a producer for mapping ipv4 to mac address.


## How ARP work ?
when new machine connected to a network and new packet arrived to the network gateway, gateway must route that packet to the right machine in the network, at the first step gateway look at the ARP mapping table that have if there is any record relevent to the packet destination ip, take that record and sent packet to mac address of the record, but if there is no record relevent to dst (destination) ip gateway broadcast an ARP request to all machines in the network then if one of those machines in the network have that ARP.request.ip <br> _Reply_: `Heyyyy i am your requested ip and this is my mac address. `

### Find list of all arp records:
```sh title="arp tool in linux"
arp -a                               # list of all records
? (192.168.1.4) at 11:22:33:44:55:66 [ether] PERM on wlp1s0
_gateway (192.168.1.1) at 30:a2:20:9d:b9:9c [ether] on wlp1s0
```
```
```

| ARP cache entry state &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |	meaning	 | action if used |
|:--------------------- |----------|----------------|
|permanent              |never expires; never verified    |reset use counter|
noarp                   |normal expiration; never verified|reset use counter
reachable               |normal expiration	              |reset use counter
stale                   |still usable; needs verification	|reset use counter; change state to delay
delay                   |schedule ARP request; needs verification|	reset use counter
probe                   |sending ARP request	            |reset use counter
incomplete              |first ARP request sent	          |send ARP request
failed                  |no response received	            |send ARP request


### Create permenent arp record:
```sh
arp -s 192.168.1.2 11:22:33:44:55:66 # add new persist record
```
#### Result:
```sh title="ip neigh"
192.168.1.4 dev wlp1s0 lladdr 11:22:33:44:55:66 PERMANENT <---
192.168.1.1 dev wlp1s0 lladdr 40:a2:20:99:99:99 REACHABLE
```



### Remove any record from cache table:
```sh title="Remove record"
arp -d 192.168.1.2 11:22:33:44:55:66
```

[ARP cache poisoning](http://localhost:8000/security/attacks/arp-cache-poisoning/){ .md-button }
[ARP spoofing](http://localhost:8000/security/attacks/arp-spoofing/){ .md-button }