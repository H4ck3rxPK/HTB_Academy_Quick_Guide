## <span style=color:red>**Host Discovery**</span>
### Using File List to Host Discovery
```go
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5
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
sudo nmap 10.129.2.18 -sn -oA host -PE --packet-trace --disable-arp-ping 
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
xsltproc [nmap.xml] -o target.html
```

### Verbose


```go!
sudo nmap 10.129.2.28 -p- -sV -v 
```

```go!
sudo nmap 10.129.2.28 -p- -sV --stats-every=5s
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
sudo nmap 10.129.2.28 -p 25 --script banner,smtp-commands
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
sudo nmap 10.129.2.28 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace
```

### Decoys
```go!
sudo nmap 10.129.2.28 -p 80 -sS -Pn -n --disable-arp-ping --packet-trace -D RND:5
```

```go
| Options                       | Description                                                                                        |
| ----------------------------- | -------------------------------------------------------------------------------------------------- |
| -D RND:5                      | Generates five random IP addresses that indicates the source IP the connection comes from. |
```

### DNS Proxying
```go!                 
sudo nmap 10.129.2.28 -p50000 -sS -Pn -n --disable-arp-ping --packet-trace --source-port 53
```