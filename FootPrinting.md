

> ## <span style=color:red>**FTP**</span> <sub><small>**TCP/21**</small></sub>
```go!
anonymous
```
> ## <span style=color:red>**SMB**</span> <sub><small>**TCP/443 UDP/139**</small></sub>
### nmap
```go!
sudo nmap  10.129.212.72 -p139,445 -script=smb-os-discovery

Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-05 16:56 BST
Nmap scan report for 10.129.212.72
Host is up (0.0024s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Nmap done: 1 IP address (1 host up) scanned in 0.26 seconds
```
### Provide arguments to scripts
```go!
nmap -v 10.129.212.72 -p139,445 --script=smb-vuln-* --script-args=unsafe=1

Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-05 17:06 BST
Nmap scan report for 10.129.212.72
Host is up (0.0037s latency).

PORT    STATE SERVICE
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

Host script results:
|_smb-vuln-ms10-054: ERROR: Script execution failed (use -d to debug)
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: ERROR: Server returned less data than it was supposed to (one or more fields are missing); aborting [9]

Nmap done: 1 IP address (1 host up) scanned in 5.16 seconds
```
### [rpcclient](https://www.geeksforgeeks.org/remote-procedure-call-rpc-in-operating-system/)
```go!
rpcclient -U "" 10.129.14.128

Enter WORKGROUP\'s password:
```
```go
| Parameters              | Description                                                         |
| ----------------------- | ------------------------------------------------------------------- |
| srvinfo                 | Server information.                                                 |
| ----------------------- | ------------------------------------------------------------------- |
| enumdomains             | Enumerate all domains that are deployed in the network.             |
| ----------------------- | ------------------------------------------------------------------- |
| querydominfo            | Provides domain, server, and user information of deployed domains.  |
| ----------------------- | ------------------------------------------------------------------- |
| netshareenumall         | Enumerates all available shares.                                    |
| ----------------------- | ------------------------------------------------------------------- |
| netsharegetinfo <share> | Provides information about a specific share.                        |
| ----------------------- | ------------------------------------------------------------------- |
| enumdomusers            | Enumerates all domain users.                                        |
| ----------------------- | ------------------------------------------------------------------- |
| queryuser <RID>         | Provides information about a specific user.                         |
| ----------------------- | ------------------------------------------------------------------- |	
```
### smbmap
```go!
sudo smbmap -H 10.129.14.128
```
### smbclient
```bash!
smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```
### [Impacket script for SMB](https://github.com/fortra/impacket/blob/master/examples/samrdump.py)

### <span style=color:darkcyan>Writeup</span>
```go!
sudo smbmap -H 10.129.14.128
Disk         Permissions	Comment
----         -----------	-------
print$       NO ACCESS	    Printer Drivers
sambashare   READ ONLY	    InFreight SMB v3.1
IPC$         NO ACCESS	    IPC Service (InlaneFreight SMB server (Samba, Ubuntu))


smbclient //10.129.212.72/sambashare
Password for [WORKGROUP\htb-ac-877925]:
Try "help" to get a list of possible commands.
smb: \>


smb: \> dir
  .                                   D        0  Mon Nov  8 13:43:14 2021
  ..                                  D        0  Mon Nov  8 15:53:19 2021
  .profile                            H      807  Tue Feb 25 12:03:22 2020
  contents                            D        0  Mon Nov  8 13:43:45 2021
  .bash_logout                        H      220  Tue Feb 25 12:03:22 2020
  .bashrc                             H     3771  Tue Feb 25 12:03:22 2020

		4062912 blocks of size 1024. 506112 blocks available

smb: \> cd contents\
smb: \contents\> dir
  .                                   D        0  Mon Nov  8 13:43:45 2021
  ..                                  D        0  Mon Nov  8 13:43:14 2021
  flag.txt                            N       38  Mon Nov  8 13:43:45 2021

		4062912 blocks of size 1024. 506112 blocks available

smb: \contents\> get flag.txt
getting file \contents\flag.txt of size 38 as flag.txt (3.1 KiloBytes/sec) (average 3.1 KiloBytes/sec)


rpcclient -U "" 10.129.212.72
Password for [WORKGROUP\]:


rpcclient $> enumdomains
name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo
Domain:		DEVOPS
Server:		DEVSMB
Comment:	InlaneFreight SMB server (Samba, Ubuntu)
Total Users:	0
Total Groups:	0
Total Aliases:	0
Sequence No:	1693935482
Force Logoff:	-1
Domain Server State:	0x1
Server Role:	ROLE_DOMAIN_PDC
Unknown 3:	0x1

rpcclient $> netshareenumall
netname: print$
	remark:	Printer Drivers
	path:	C:\var\lib\samba\printers
	password:	
netname: sambashare
	remark:	InFreight SMB v3.1
	path:	C:\home\sambauser\
	password:	
netname: IPC$
	remark:	IPC Service (InlaneFreight SMB server (Samba, Ubuntu))
	path:	C:\tmp
	password:	
```

> ## [<span style=color:red>**NFS**</span> <sub><small>**TCP/111,2049**</small></sub>](https://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html)
### nmap
```go!
sudo nmap 10.129.202.72 -p111,2049 -sV -Pn --disable-arp-ping --script=nfs*

Starting Nmap 7.93 ( https://nmap.org ) at 2023-09-06 08:36 BST
Nmap scan report for 10.129.202.72
Host is up.

PORT     STATE    SERVICE VERSION
111/tcp  filtered rpcbind
2049/tcp filtered nfs

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 3.30 seconds
```
#### <span style=color:darkred>Mounting NFS share</span>
```go!
H4ck3rxPK@htb[/htb]$ mkdir target-NFS
H4ck3rxPK@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
H4ck3rxPK@htb[/htb]$ cd target-NFS
H4ck3rxPK@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```
#### <span style=color:darkred>Unmounting</span>
```go!
sudo umount ./target-NFS/
```

> ## <span style=color:red>**DNS**</span> <sub><small>**TCP/53**</small></sub>
![](https://hackmd.io/_uploads/HyvdKnrRn.png)
### <span style=color:darkblue>Linux System Configuration Files</span> 
```go
  ---------------------- | ---------------------------------------------------  
| /etc/hosts             | Query IP-hostname mapping from the table             |
| ---------------------- | ---------------------------------------------------  |
| /etc/resolv.conf       | Query domain from nameserver (DNS Server)            |
  ---------------------- | ---------------------------------------------------

``` 
#### <span style=color:darkred>Dig - SOA Query</span>
```go
H4ck3rxPK@htb[/htb]$ dig soa www.inlanefreight.com

; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.inlanefreight.com.         IN      SOA

;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Jan 05 12:56:10 GMT 2023
;; MSG SIZE  rcvd: 128
```
#### <span style=color:darkred>Dig - NS Query</span>
```go
H4ck3rxPK@htb[/htb]$ dig ns inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> ns inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45010
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: ce4d8681b32abaea0100000061475f73842c401c391690c7 (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      NS

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:04:03 CEST 2021
;; MSG SIZE  rcvd: 107
```
#### <span style=color:darkred>Dig - Version Query</span>
```go
H4ck3rxPK@htb[/htb]$ dig CH TXT version.bind 10.129.120.85

; <<>> DiG 9.10.6 <<>> CH TXT version.bind
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47786
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1"

;; ADDITIONAL SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1-Debian"

;; Query time: 2 msec
;; SERVER: 10.129.120.85#53(10.129.120.85)
;; WHEN: Wed Jan 05 20:23:14 UTC 2023
;; MSG SIZE  rcvd: 101
```
#### <span style=color:darkred>Dig - Any Query</span>
```go
H4ck3rxPK@htb[/htb]$ dig any inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7649
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 064b7e1f091b95120100000061476865a6026d01f87d10ca (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:42:13 CEST 2021
;; MSG SIZE  rcvd: 437
```
### Zone Transfer - AXFR
#### <span style=color:darkred>Dig - AXFR Query</span>
```go
H4ck3rxPK@htb[/htb]$ dig axfr inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr inlanefreight.htb @10.129.14.128
;; global options: +cmd
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
internal.inlanefreight.htb. 604800 IN   A       10.129.1.6
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 4 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:51:19 CEST 2021
;; XFR size: 9 records (messages 1, bytes 520)
```
### <span style=color:darkcyan>Writeup</span>
- First, I try **dig axfr** to target, and I get the subdomain and NS
```go
dig axfr inlanefreight.htb @10.129.27.99

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr inlanefreight.htb @10.129.27.99
;; global options: +cmd
inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.	604800	IN	TXT	"MS=ms97310371"
inlanefreight.htb.	604800	IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.	604800	IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
app.inlanefreight.htb.	604800	IN	A	10.129.18.15
dev.inlanefreight.htb.	604800	IN	A	10.12.0.1
internal.inlanefreight.htb. 604800 IN	A	10.129.1.6
mail1.inlanefreight.htb. 604800	IN	A	10.129.18.201
ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 0 msec
;; SERVER: 10.129.27.99#53(10.129.27.99) (TCP)
;; WHEN: Sat Sep 23 09:41:42 BST 2023
;; XFR size: 11 records (messages 1, bytes 560)
```
- Then, I attempted to perform a Zone Transfer on each subdomain individually, such as app.inlanefreight.htb, dev.inlanefreight.htb, and internal.inlanefreight.htb. Upon doing so, I discovered that the subdomain <span style=color:red>**internal.inlanefreight.htb**</span> responsed the **second** and **third flag(DC1)**.

```go
dig axfr internal.inlanefreight.htb @10.129.27.99

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr internal.inlanefreight.htb @10.129.27.99
;; global options: +cmd
internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
internal.inlanefreight.htb. 604800 IN	TXT	"MS=ms97310371"
internal.inlanefreight.htb. 604800 IN	TXT	"HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34}"
internal.inlanefreight.htb. 604800 IN	TXT	"atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
internal.inlanefreight.htb. 604800 IN	TXT	"v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
internal.inlanefreight.htb. 604800 IN	NS	ns.inlanefreight.htb.
dc1.internal.inlanefreight.htb.	604800 IN A	10.129.34.16
dc2.internal.inlanefreight.htb.	604800 IN A	10.129.34.11
mail1.internal.inlanefreight.htb. 604800 IN A	10.129.18.200
ns.internal.inlanefreight.htb. 604800 IN A	127.0.0.1
vpn.internal.inlanefreight.htb.	604800 IN A	10.129.1.6
ws1.internal.inlanefreight.htb.	604800 IN A	10.129.1.34
ws2.internal.inlanefreight.htb.	604800 IN A	10.129.1.35
wsus.internal.inlanefreight.htb. 604800	IN A	10.129.18.2
internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 3 msec
;; SERVER: 10.129.27.99#53(10.129.27.99) (TCP)
;; WHEN: Sat Sep 23 09:47:58 BST 2023
;; XFR size: 15 records (messages 1, bytes 677)
```
- Finally, I attempted a zone transfer on all subdomains using <span style=color:darkred>**AXFR**</span> continue. However, I received a "transfer failed" response, leading me to believe that other <span style=color:darkred>**AXFR**</span> queries might be on a banlist.
```go
dig axfr dev.inlanefreight.htb

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr dev.inlanefreight.htb
;; global options: +cmd
; Transfer failed.
```
- Subsequently, I used <span style=color:red>**gobuster**</span> with <span style=color:darkred>**multiple dictionaries**</span> *~~(p.s that is not point, and very waste time)~~* to identify potential subdomains.
```go
gobuster dns -d dev.inlanefreight.htb -w /usr/share/SecLists/Discovery/DNS/fierce-hostlist.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Domain:     dev.inlanefreight.htb
[+] Threads:    10
[+] Timeout:    1s
[+] Wordlist:   /usr/share/SecLists/Discovery/DNS/fierce-hostlist.txt
===============================================================
2023/10/18 03:31:57 Starting gobuster in DNS enumeration mode
===============================================================
2023/10/18 03:31:57 [-] Unable to validate base domain: dev.inlanefreight.htb (lookup dev.inlanefreight.htb on 10.129.121.62:53: no such host)
Found: dev1.dev.inlanefreight.htb
Found: ns.dev.inlanefreight.htb  
Found: win2k.dev.inlanefreight.htb

===============================================================
2023/10/18 03:31:59 Finished
===============================================================
```
- I think that <span style=color:red>**win2k**</span> is my target, so I use the dig to explore it further and retrieve the final **flag（x.x.x.203 FQDN）**
```go
dig win2k.dev.inlanefreight.htb @10.129.27.99

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> win2k.dev.inlanefreight.htb
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10143
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 396058d8b1ccd99b01000000652f3e0681ed4fde6b60e9d0 (good)
;; QUESTION SECTION:
;win2k.dev.inlanefreight.htb.	IN	A

;; ANSWER SECTION:
win2k.dev.inlanefreight.htb. 604800 IN	A	10.12.3.203

;; Query time: 13 msec
;; SERVER: 10.129.121.62#53(10.129.121.62) (UDP)
;; WHEN: Wed Oct 18 03:08:04 BST 2023
;; MSG SIZE  rcvd: 100
```

> ## <span style=color:red>**SMTP**</span> <sub><small>**TCP/25 587 465(encryption)**</small></sub>
![](https://hackmd.io/_uploads/SkuyJRnZT.png)

#### Identification protocol:
- [DomainKeys (DKIM)](https://dkim.org/)
- [Sender Policy Framework (SPF)](https://dmarcian.com/what-is-spf/).

```go
| Parameters     | Description                                                                           |
| -------------- | ------------------------------------------------------------------------------------- |
| AUTH PLAIN     | AUTH is a service extension used to authenticate the client.   
| -------------- | ------------------------------------------------------------------------------------- |
| HELO           | The client logs in with its computer name and thus starts the session.                           |
| -------------- | ----------------------------------------------------------------------                |
| MAIL FROM      | The client names the email sender.                                                               |
| -------------- | ----------------------------------------------------------------------                |
| RCPT TO        | The client names the email recipient.                                                            |
| -------------- | ----------------------------------------------------------------------                |
| DATA           | The client initiates the transmission of the email.                                              |
| -------------- | ----------------------------------------------------------------------                |
| RSET           | The client aborts the initiated transmission but keeps the connection between client and server. |
| -------------- | ----------------------------------------------------------------------                |
| VRFY           | The client checks if a mailbox is available for message transfer.                                |
| -------------- | ----------------------------------------------------------------------                |
| EXPN           | The client checks if a mailbox is available for messaging with this command.                     |
| -------------- | ----------------------------------------------------------------------                |
| NOOP           | The client requests a response from the server to prevent disconnection due to time-out.         |
| -------------- | ----------------------------------------------------------------------                |
| QUIT           | The client terminates the session.                                                               |
| -------------- | ----------------------------------------------------------------------                |
```

> ## <span style=color:red>**IMAP/POP3**</span> <sub><small>**TCP/110 143 993(SSL) 995(SSL)**</small></sub>

### <span style=color:darkcyan>Writeup</span>
**Recon**
```go
sudo nmap 10.129.253.231 -sV -Pn -n --disable-arp-ping -p 110,143,993,995 -sC
Starting Nmap 7.93 ( https://nmap.org ) at 2023-10-31 07:59 GMT
Nmap scan report for 10.129.253.231
Host is up (0.0097s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
| Not valid before: 2021-11-08T23:10:05
|_Not valid after:  2295-08-23T23:10:05
|_pop3-capabilities: UIDL CAPA TOP STLS PIPELINING AUTH-RESP-CODE SASL RESP-CODES
143/tcp open  imap     Dovecot imapd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
| Not valid before: 2021-11-08T23:10:05
|_Not valid after:  2295-08-23T23:10:05
|_imap-capabilities: post-login listed LOGINDISABLEDA0001 ID SASL-IR ENABLE more LOGIN-REFERRALS STARTTLS capabilities OK have IMAP4rev1 Pre-login LITERAL+ IDLE
993/tcp open  ssl/imap Dovecot imapd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
| Not valid before: 2021-11-08T23:10:05
|_Not valid after:  2295-08-23T23:10:05
|_imap-capabilities: post-login AUTH=PLAINA0001 ID SASL-IR ENABLE more LOGIN-REFERRALS OK capabilities listed have IMAP4rev1 Pre-login IDLE LITERAL+
995/tcp open  ssl/pop3 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: UIDL CAPA TOP USER PIPELINING AUTH-RESP-CODE SASL(PLAIN) RESP-CODES
| ssl-cert: Subject: commonName=dev.inlanefreight.htb/organizationName=InlaneFreight Ltd/stateOrProvinceName=London/countryName=UK
| Not valid before: 2021-11-08T23:10:05
|_Not valid after:  2295-08-23T23:10:05

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.79 seconds
```

Got the info to question1 and 2 

#### <span style=color:darkred>Netcat</span>
##### imap 143
```go
nc 10.129.253.231 143
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ STARTTLS LOGINDISABLED] HTB{roncfbw7iszerd7shni7jr2343zhrj}
```
##### pop3 110
```go
nc 10.129.253.231 110
+OK InFreight POP3 v9.188
```
<span style=color:darkgreen>**GPT**</span>

```dockerfile
123
```