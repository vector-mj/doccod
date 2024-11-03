# Iptables

# Iptables (How it works and how we can configure it)
---

![packet flow](https://www.lumiun.com/blog/wp-content/uploads/Firewall-sua-rede-precisa-dessa-protecao-blog.jpg)


#### How to discover all used ports
- You can use `ss` tools in linux to find out open ports in your system.
- You should check all of these ports and you ought to specify want you wanna to do with these ports.

#### Things that you should consider
- Your services
    * `0.0.0.0:1080 | listen      | proxy server`
    * `0.0.0.0:9100 | listen      | node exporter`
    * `0.0.0.0:22   | listen      | ssh daemon`
- Internet and established connections
    * `8.8.8.8:443  | established | you connected to a website (internet)`
- Inter process communications
    * `loopback interface`
- Block ping 


#### Iptables chains
- PREROUTING
- FORWARD
- INPUT
- OUTPUT
- POSTROUTING

#### Iptables tables (within chains)
- raw
- mangle
- nat
- filter
- security

#### Iptables flags
```yaml
# Specification
-t : table name

# Operations
-A <CHAIN_NAME> : append
-I <CHAIN_NAME> : insert
-D <CHAIN_NAME> : delete

-s : src addr | ip addr or block
-d : dst addr | ip addr or block

-i <IFACE_NAME> : in  interface
-o <IFACE_NAME> : out interface

-p <PROTOCOL> : tcp | udp | icmp | /etc/protocols | zero mean all 
-c <PACKET> <BYTES> : initalizing counter values
# Target
-j <TARGET> : MASQUERADE | DROP | LOG | ACCEPT | REJECT | REDIRECT | MARK | MIRROR

# Auxilary
-P <CHAIN_NAME> : default policy of chain
-Z <CHAIN_NAME> : reset packet counters
-F <CHAIN_NAME> : flush rules
-p <PROTOCOL> -h : help note
```


#### What is Masquerading
- IP Masquerading, helps machines with non-routable IP addresses to access the Internet via the machine that is masquerading. It is a form of NAT (Network Address Translation).

#### Examples

```sh
echo 1 > /proc/sys/net/ipv4/ip_forward
iptables -P FORWARD ACCEPT
iptables -t nat -A POSTROUTING -j MASQUERADE
iptables -t nat -A PREROUTING -p tcp -j REDIRECT --to-ports 4545
----------------------------------------------------------------------
iptables -t nat -A OUTPUT -p tcp -m owner ! --uid-owner proxy -j REDIRECT --to-ports 4545
iptables -t nat -I OUTPUT -p tcp -m owner --uid-owner root -j RETURN
----------------------------------------------------------------------
iptables -P INPUT DROP
iptables -A INPUT -p tcp --sport 80 -j ACCEPT
iptables -A INPUT -p tcp --sport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A INPUT -p udp --sport 53 -j ACCEPT
iptables -A INPUT -p tcp --sport 53 -j ACCEPT
----------------------------------------------------------------------
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i eth0 -p udp -m multiport --sport 53,67 -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i eth0 -p tcp -m multiport --sport 53,80 -m state --state ESTABLISHED,RELATED -j ACCEPT
----------------------------------------------------------------------
EXTIF="eth0"
EXTIP="192.168.111.140"
UNIVERSE="0.0.0.0/0"
iptables -A INPUT -i $EXTIF -s $UNIVERSE -d $EXTIP -m state --state ESTABLISHED,RELATED -j ACCEPT
----------------------------------------------------------------------
iptables -A INPUT -i lo -j LOG --log-prefix "ILO:" --log-level info # /var/log/syslog
iptables -A INPUT -j LOG --log-prefix "IDROP:" --log-level debug    # /var/log/syslog
----------------------------------------------------------------------
iptables -t nat    -A PREROUTING  -p tcp -d 15.45.23.67 --dport 80 -j DNAT --to-destination 192.168.1.1-192.168.1.10
iptables -t nat    -A POSTROUTING -p tcp --dst $HTTP_IP --dport 80 -j SNAT --to-source $LAN_IP
iptables -t mangle -A FORWARD -p tcp --dport 80 -j DSCP --set-dscp 1
----------------------------------------------------------------------
iptables -A OUTPUT -p icmp -o eth0 -j ACCEPT          
iptables -A INPUT  -p icmp --icmp-type echo-reply -s 0/0 -i eth0 -j ACCEPT     
iptables -A INPUT  -p icmp --icmp-type destination-unreachable -s 0/0 -i eth0 -j ACCEPT  
iptables -A INPUT  -p icmp --icmp-type time-exceeded -s 0/0 -i eth0 -j ACCEPT       
iptables -A INPUT  -p icmp -i eth0 -j DROP  
----------------------------------------------------------------------

```

#### Useful links
- https://www.frozentux.net/iptables-tutorial/chunkyhtml