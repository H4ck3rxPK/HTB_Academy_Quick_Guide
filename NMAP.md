## <span style=color:red>**Host Discovery**</span>
### Using File List to Host Discovery
```go
H4ck3rxPK@htb[/htb]$ cat hosts.lst
10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28

H4ck3rxPK@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
10.129.2.18
10.129.2.19
10.129.2.20
```


```go
| Options  | Description                                                         |
| -------- | ------------------------------------------------------------------- |
| -sn      | Disables port scanning                                              |
| -------- | ------------------------------------------------------------------- |
| -oA tnet | Stores the results in all formats starting with the name 'tnet'     |
| -------- | ------------------------------------------------------------------- |
| -iL      | Performs defined scans against targets in provided 'hosts.lst' list |
``` 	

### Trace the Packets using ICMP
```go
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping 

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-15 00:12 CEST
SENT (0.0107s) ICMP [10.10.14.2 > 10.129.2.18 Echo request (type=8/code=0) id=13607 seq=0] IP [ttl=255 id=23541 iplen=28 ]
RCVD (0.0152s) ICMP [10.129.2.18 > 10.10.14.2 Echo reply (type=0/code=0) id=13607 seq=0] IP [ttl=128 id=40622 iplen=28 ]
Nmap scan report for 10.129.2.18
Host is up (0.086s latency).
MAC Address: DE:AD:00:00:BE:EF
Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds
```

```go
| Options            | Description                                                              |
| ------------------ | ------------------------------------------------------------------------ |
| -sn                | Disables port scanning                                                   |
| ------------------ | ------------------------------------------------------------------------ |
| -oA tnet           | Stores the results in all formats starting with the name 'host'          |
| ------------------ | ------------------------------------------------------------------------ |
| -PE                | Performs the ping scan by using 'ICMP Echo requests' against the target. |
| ------------------ | ------------------------------------------------------------------------ |
| --reason           | Displays the reason for specific result.                                 |
| ------------------ | ------------------------------------------------------------------------ |
| --disable-arp-ping | Disables ARP ping.                                                       |
``` 	

### Time-To-Live(TTL)
#### 128=Windows // 64=Linux // 64/123=MacOS
---
## <span style=color:red>**Port Scan**</span>

![](https://hackmd.io/_uploads/Sy4xoOg92.png)

### Saving the Results(-oA) and Check

```go!
H4ck3rxPK@htb[/htb]$ xsltproc [target.xml] -o target.html
```
![](https://hackmd.io/_uploads/rJ8M6Ox53.png)

### Verbose


```go!
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV -v 
```

```go!
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p- -sV --stats-every=5s
```

```go
| Options            | Description                                                                    |
| ------------------ | ------------------------------------------------------------------------------ |
| -v                 | Increases the verbosity of the scan, which displays more detailed information. |
| ------------------ | ------------------------------------------------------------------------       |
| --stats-every=5s   | Shows the progress of the scan every 5 seconds.                                |
```

### [**Specifying Scripts**](https://nmap.org/nsedoc/scripts/)
```go!
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-16 23:21 CEST
Nmap scan report for 10.129.2.28
Host is up (0.050s latency).

PORT   STATE SERVICE
25/tcp open  smtp
|_banner: 220 inlane ESMTP Postfix (Ubuntu)
|_smtp-commands: inlane, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8,
MAC Address: DE:AD:00:00:BE:EF (Intel Corporate)
```

```go
| Options                       | Description                                                                                        |
| ----------------------------- | -------------------------------------------------------------------------------------------------- |
| -A                            | Performs service detection, OS detection, traceroute and uses defaults scripts to scan the target. |
| ----------------------------  | ---------------------------------------------------------------------------------------------------|
| --script banner,smtp-commands | Uses specified NSE scripts.                                                                        |
```
## <span style=color:red>**Firewall and IDS/IPS Evasion**</span>

### ACK Scan
```go!
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

### Decoys
```go!
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

```go
| Options                       | Description                                                                                        |
| ----------------------------- | -------------------------------------------------------------------------------------------------- |
| -D RND:5                      | Generates five random IP addresses that indicates the source IP the connection comes from. |
```

### DNS Proxying
```go!                 
H4ck3rxPK@htb[/htb]$ sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```

## <span style=color:purple><big>**CheatSheet**</big></span>
### Scanning Options

| **Nmap Option** | **Description** |
|---|----|
| `10.10.10.0/24` | Target network range. |
| `-sn` | Disables port scanning. |
| `-Pn` | Disables ICMP Echo Requests |
| `-n` | Disables DNS Resolution. |
| `-PE` | Performs the ping scan by using ICMP Echo Requests against the target. |
| `--packet-trace` | Shows all packets sent and received. |
| `--reason` | Displays the reason for a specific result. |
| `--disable-arp-ping` | Disables ARP Ping Requests. |
| `--top-ports=<num>` | Scans the specified top ports that have been defined as most frequent.  |
| `-p-` | Scan all ports. |
| `-p22-110` | Scan all ports between 22 and 110. |
| `-p22,25` | Scans only the specified ports 22 and 25. |
| `-F` | Scans top 100 ports. |
| `-sS` | Performs an TCP SYN-Scan. |
| `-sA` | Performs an TCP ACK-Scan. |
| `-sU` | Performs an UDP Scan. |
| `-sV` | Scans the discovered services for their versions. |
| `-sC` | Perform a Script Scan with scripts that are categorized as "default". |
| `--script <script>` | Performs a Script Scan by using the specified scripts. |
| `-O` | Performs an OS Detection Scan to determine the OS of the target. |
| `-A` | Performs OS Detection, Service Detection, and traceroute scans. |
| `-D RND:5` | Sets the number of random Decoys that will be used to scan the target. |
| `-e` | Specifies the network interface that is used for the scan. |
| `-S 10.10.10.200` | Specifies the source IP address for the scan. |
| `-g` | Specifies the source port for the scan. |
| `--dns-server <ns>` | DNS resolution is performed by using a specified name server. |

### Output Options

| **Nmap Option** | **Description** |
|---|----|
| `-oA filename` | Stores the results in all available formats starting with the name of "filename". |
| `-oN filename` | Stores the results in normal format with the name "filename". |
| `-oG filename` | Stores the results in "grepable" format with the name of "filename". |
| `-oX filename` | Stores the results in XML format with the name of "filename". |

### Performance Options

| **Nmap Option** | **Description** |
|---|----|
| `--max-retries <num>` | Sets the number of retries for scans of specific ports. |
| `--stats-every=5s` | Displays scan's status every 5 seconds. |
| `-v/-vv` | Displays verbose output during the scan. |
| `--initial-rtt-timeout 50ms` | Sets the specified time value as initial RTT timeout. |
| `--max-rtt-timeout 100ms` | Sets the specified time value as maximum RTT timeout. |
| `--min-rate 300` | Sets the number of packets that will be sent simultaneously. |
| `-T <0-5>` | Specifies the specific timing template. |
