# Netplan

### Netplan
```yaml
network:
version: 2
renderer: networkd

ethernets:
    eth0:
    dhcp4: no
    addresses:
        - 192.168.200.111/24
    # gateway4: 192.168.200.1
    routes:
        - to: default
        via: 192.168.200.1
    nameservers:
        addresses:
        - 1.1.1.1
    set-name: eth0
    match:
        name: eth*
        macaddress: 11:22:33:AA:BB:FF

bonds:
    bond0:
    addresses:
        - 185.235.41.235
    interfaces:
        - eth0
    nameservers:
        search:
        - local
        addresses:
        - 1.1.1.1
    mtu: 1500
    parameters:
        mode: 802.3ad
        lacp-rate: fast
        mii-monitor-interval: 100
        transmit-hash-policy: layer3+4

bridges:
    br0:
    dhcp4: no
    addresses:
        - 192.168.2.6/24
    interfaces:
        - vlan2

vlans:
    vlan1:
    id: 10
    link: eth0
    addresses: [ "10.3.98.5/24" ]
    nameservers:
        addresses: [ "127.0.0.1" ]

lo:
    match:
    name: lo

wifis:
    wlp2s0b1:
    dhcp4: no
    dhcp6: no
    addresses: [192.168.0.21/24]
    nameservers:
        addresses: [192.168.0.1, 8.8.8.8]
    access-points:
        "network_ssid_name":
        password: "**********"
    routes:
        - to: default
        via: 192.168.0.1

    wl0:
    access-points:
        university:
        auth:
            key-management: eap
            method: tls
            anonymous-identity: "@cust.example.com"
            identity: "cert-joe@cust.example.com"
            ca-certificate: /etc/ssl/cust-cacrt.pem
            client-certificate: /etc/ssl/cust-crt.pem
            client-key: /etc/ssl/cust-key.pem
            client-key-password: "d3cryptPr1v4t3K3y"
    dhcp4: yes
```

### Interfaces
```yaml
    iface eth0 inet static
    address 192.168.1.5
    netmask 255.255.255.0
    gateway 192.168.1.254

    auto eth0
    iface eth0 inet dhcp
```