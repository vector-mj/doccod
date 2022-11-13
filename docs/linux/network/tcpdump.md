---
comments: true
---

![tcpdump](../../assets/tcpdump.jpg){width=700}

## What is TcpDump ?
TcpDump is a tool for monitoring and sniffing data that are on network interface card, for example if you have issue for connecting to another system in your network or you want to trace all type of requests and responces in your network, tcpdump is your hero in this type of situations.
## Tcp Flags
```yaml
Unskilled Attackers Pester Real Security Folks
==============================================
                     TCPDUMP FLAGS
Unskilled =  URG  =  (Not Displayed in Flag Field, Displayed elsewhere) 
Attackers =  ACK  =  (Not Displayed in Flag Field, Displayed elsewhere)
Pester    =  PSH  =  [P] (Push Data)
Real      =  RST  =  [R] (Reset Connection)
Security  =  SYN  =  [S] (Start Connection)
Folks     =  FIN  =  [F] (Finish Connection)
          SYN-ACK =  [S.] (SynAcK Packet)
                     [.] (No Flag Set)
```
## Tcpdump commands 

```sh title="🟢 List of NICs"
tcpdump -D
```
```sh title="🟢 Count of packets for sniffing"
tcpdump -c200
```
```sh title="🟢 Select interface card"
tcpdump -i 3
```
```sh title="🟢 Dont resolve hostname"
tcpdump -n
```
```sh title="🟢 Dont resolve portname"
tcpdump -nn
```
```sh title="🟢 Specifi packet size to capture"
tcpdump -s100 # 100 byte
```
```sh title="🟢 Verbose information"
tcpdump -vvv
```
```sh title="🟢 Hex output"
tcpdump -X
```
```sh title="🟢 Hex output with ethernet header"
tcpdump -XX
```
```sh title="🟢 Write result on file (pcap) with maxsize of file"
tcpdump -w ./test.pcap -C 2 -G 6 # 2 megabyte, rotate every 6 sec
```
```sh title="🟢 Read data from pcap file"
tcpdump -r ./test.pcap
```
```sh title="🟢 Display ASCII format"
tcpdump -A
```
```sh title="🟢 Quiet mode"
tcpdump -q
```
```sh title="🟢 Get timestamp with packet number"
tcpdump -ttt -#
```
```sh title="🟢 Decrypt IPSEC traffic "
tcpdump -E 
```
```sh title="🟢 Pipe output "
tcpdump -l | grep XXXX
```
```sh title="🟢 Directino of captured packets (in|out)"
tcpdump -Q out
```
```sh title="🟢 Monitor mode"
tcpdump -I
```
```sh title="🟢 Ethernet header"
tcpdump -e
```
```sh title="🟢 Tcp sequence number"
tcpdump -S
```

### Tcpdump Examples


```yaml
##USAGE##
Basic communication and pipe output for grep:
tcpdump -nS -l | grep Host

Basic communication (very verbose):
tcpdump -nnvvS

A deeper look at the traffic:
tcpdump -nnvvXS

Heavy packet viewing:
tcpdump -nnvvXSs 1514

look for traffic based on IP address: 
tcpdump host 1.2.3.4

find traffic from only a source or destination:
tcpdump src 2.3.4.5 
tcpdump dst 3.4.5.6

capture an entire network using CIDR notation: 
tcpdump net 1.2.3.0/24

works for tcp, udp, and icmp: 
tcpdump icmp

see only traffic to or from a certain port:
tcpdump port 3389

filter based on the source or destination port: 
tcpdump src port 1025 # tcpdump dst port 389

src/dst, port, protocol:
tcpdump src port 1025 and tcp 
tcpdump udp and src port 53

see traffic to any port in a range: 
tcpdump portrange 21-23

only see packets below or above a certain size: 
tcpdump less 32 
tcpdump greater 128

filtering for size using symbols:
tcpdump > 32 
tcpdump <= 128

[ Note: Only the PSH, RST, SYN, and FIN flags are displayed in tcpdump‘s flag field output. URGs and ACKs are displayed, but they are shown elsewhere in the output rather than in the flags field ]

Keep in mind the reasons these filters work. The filters above find these various packets because tcp[13] looks at offset 13 in the TCP header, the number represents the location within the byte, and the !=0 means that the flag in question is set to 1, i.e. it’s on.

Show all URG packets:
tcpdump 'tcp[13] & 32 != 0'

Show all ACK packets:
tcpdump 'tcp[13] & 16 != 0'

Show all PSH packets:
tcpdump 'tcp[13] & 8 != 0'

Show all RST packets:
tcpdump 'tcp[13] & 4 != 0'

Show all SYN packets:
tcpdump 'tcp[13] & 2 != 0'

Show all FIN packets:
tcpdump 'tcp[13] & 1 != 0'

Show all SYN-ACK packets:
tcpdump 'tcp[13] = 18'

Show icmp echo request and reply:
tcpdump -n icmp and 'icmp[0] != 8 and icmp[0] != 0'

Show all IP packets with a non-zero TOS field (one byte TOS field is at offset 1 in IP header):
tcpdump -v -n ip and ip[1]!=0

Show all IP packets with TTL less than some value (on byte TTL field is at offset 8 in IP header):
tcpdump -v ip and 'ip[8]<2'

Show TCP SYN packets:
tcpdump -n tcp and port 80 and 'tcp[tcpflags] & tcp-syn == tcp-syn'
tcpdump tcp and port 80 and 'tcp[tcpflags] == tcp-syn'
tcpdump -i <interface> "tcp[tcpflags] & (tcp-syn) != 0"

Show TCP ACK packets:
tcpdump -i <interface> "tcp[tcpflags] & (tcp-ack) != 0"

Show TCP SYN/ACK packets (typically, responses from servers):
tcpdump -n tcp and 'tcp[tcpflags] & (tcp-syn|tcp-ack) == (tcp-syn|tcp-ack)'
tcpdump -n tcp and 'tcp[tcpflags] & tcp-syn == tcp-syn' and 'tcp[tcpflags] & tcp-ack == tcp-ack'
tcpdump -i <interface> "tcp[tcpflags] & (tcp-syn|tcp-ack) != 0"

Show TCP FIN packets:
tcpdump -i <interface> "tcp[tcpflags] & (tcp-fin) != 0"

Show ARP Packets with MAC address:
tcpdump -vv -e -nn ether proto 0x0806

Show packets of a specified length (IP packet length (16 bits) is located at offset 2 in IP header):
tcpdump -l icmp and '(ip[2:2]>50)' -w - |tcpdump -r - -v ip and '(ip[2:2]<60)'

```