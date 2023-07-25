## <span style=color:red>**Passive Information Gathering**</span>
### [<span style=color:blue>**WHOIS**<SMALL> (TCP/43)</SMALL></span>](https://datatracker.ietf.org/doc/html/rfc3912)
#### Linux
```go!
H4ck3rxPK@htb[/htb]$ export TARGET="facebook.com" # Assign our target to an environment variable
H4ck3rxPK@htb[/htb]$ whois $TARGET
```

#### Windows Sysinternals
```go!
C:\htb> whois.exe facebook.com
```

## <span style=color:red>**Active Infrastructure Identification**</span>
### **whatweb**
```go!
H4ck3rxPK@htb[/htb]$ sudo whatweb -a3 -v [domain & IP]
```

### <span style=color:blue>**Nslookup for DNS**<SMALL> (UDP or TCP/53)</SMALL></span>

#### A Records
```go!
H4ck3rxPK@htb[/htb]$ export TARGET="facebook.com"
H4ck3rxPK@htb[/htb]$ nslookup $TARGE
```
#### **nslookup ---> for dns**
##### Active Subdomain Enumeration(超不懂
```go!
H4ck3rxPK@htb[/htb]$ nslookup -type=NS inlanefreight.htb 10.129.238.241
```

```go
| Parameters     | Description                                                         |
| -------------- | ------------------------------------------------------------------- |
| NS             | Name Server                                                         |
| -------------- | ------------------------------------------------------------------- |
| 10.129.238.241 | DNS Server                                                          |
```

```go!
H4ck3rxPK@htb[/htb]$ nslookup -type=any -query=AXFR inlanefreight.htb 10.129.238.241
```
```go
| Parameters     | Description                                                         |
| -------------- | ------------------------------------------------------------------- |
| AXFR           | Asynchronous Full Zone Transfer                                                         |
| -------------- | ------------------------------------------------------------------- |
| SOA            | Start of Authority                                                         |
```

```go!
nslookup -type=any -query=AXFR inlanefreight.htb 10.129.173.95 | grep Name | cut -d':' -f2 | while read line; do echo $line >> subdomain2.txt; done
```
### <span style=color:blue>**Ffuf for fuzzing**</span>
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w ./vhosts -u http://10.129.122.112 -H "HOST: FUZZ.inlanefreight.htb" -fs 612

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.4.1-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.122.112
 :: Wordlist         : FUZZ: ./vhosts
 :: Header           : Host: FUZZ.inlanefreight.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
 :: Filter           : Response size: 612
________________________________________________

support                 [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
store                   [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
blog                    [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
forum                   [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
app                     [Status: 200, Size: 103, Words: 3, Lines: 6, Duration: 3ms]
shop                    [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
dev-admin               [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 4ms]
m                       [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
my                      [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 3ms]
help                    [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 2ms]
some                    [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 4ms]
www                     [Status: 200, Size: 10918, Words: 3499, Lines: 376, Duration: 4ms]
:: Progress: [12/12] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```

### <span style=color:blue>**Curl**</span>
```go!
H4ck3rxPK@htb[/htb]$ curl -s 10.129.122.112 -H "Host:app.inlanefreight.htb"
```

## <span style=color:green>**Writeup**</span>
https://www.youtube.com/watch?v=fDT-SJPl0SY
```go!
dig @10.129.160.95 axfr inlanefreight.htb

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> @10.129.160.95 axfr inlanefreight.htb
; (1 server found)
;; global options: +cmd
inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.	604800	IN	NS	ns.inlanefreight.htb.
admin.inlanefreight.htb. 604800	IN	A	10.10.34.2
ftp.admin.inlanefreight.htb. 604800 IN	A	10.10.34.2
careers.inlanefreight.htb. 604800 IN	A	10.10.34.50
dc1.inlanefreight.htb.	604800	IN	A	10.10.34.16
dc2.inlanefreight.htb.	604800	IN	A	10.10.34.11
internal.inlanefreight.htb. 604800 IN	A	127.0.0.1
admin.internal.inlanefreight.htb. 604800 IN A	10.10.1.11
wsus.internal.inlanefreight.htb. 604800	IN A	10.10.1.240
ir.inlanefreight.htb.	604800	IN	A	10.10.45.5
dev.ir.inlanefreight.htb. 604800 IN	A	10.10.45.6
ns.inlanefreight.htb.	604800	IN	A	127.0.0.1
resources.inlanefreight.htb. 604800 IN	A	10.10.34.100
securemessaging.inlanefreight.htb. 604800 IN A	10.10.34.52
test1.inlanefreight.htb. 604800	IN	A	10.10.34.101
us.inlanefreight.htb.	604800	IN	A	10.10.200.5
cluster14.us.inlanefreight.htb.	604800 IN A	10.10.200.14
messagecenter.us.inlanefreight.htb. 604800 IN A	10.10.200.10
ww02.inlanefreight.htb.	604800	IN	A	10.10.34.112
www1.inlanefreight.htb.	604800	IN	A	10.10.34.111
inlanefreight.htb.	604800	IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 3 msec
;; SERVER: 10.129.160.95#53(10.129.160.95) (TCP)
;; WHEN: Mon Jul 24 16:25:47 BST 2023
;; XFR size: 22 records (messages 1, bytes 594)
```

- [ ] SOA (Start of Authority) record typically appears twice: 
     - once at the beginning of the results.
     - once at the end. 

- [ ] He means that 2 local host ip, there are going to be the two labs that exist on the target

```go!
dig @10.129.160.95 axfr internal.inlanefreight.htb @ns.inlanefreight.htb

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> @10.129.160.95 axfr internal.inlanefreight.htb @ns.inlanefreight.htb
; (1 server found)
;; global options: +cmd
internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
internal.inlanefreight.htb. 604800 IN	TXT	"ZONE_TRANSFER{87o2z3cno7zsoiedznxoi82z3o47xzhoi}"
internal.inlanefreight.htb. 604800 IN	NS	ns.inlanefreight.htb.
dev.admin.internal.inlanefreight.htb. 604800 IN	A 10.10.1.2
panel.admin.internal.inlanefreight.htb.	604800 IN A 10.10.1.2
printer.admin.internal.inlanefreight.htb. 604800 IN A 10.10.1.3
dc3.internal.inlanefreight.htb.	604800 IN A	10.10.1.5
ns.internal.inlanefreight.htb. 604800 IN A	127.0.0.1
ns2.internal.inlanefreight.htb.	604800 IN A	10.10.34.136
ws1.internal.inlanefreight.htb.	604800 IN A	10.10.2.11
ws2.internal.inlanefreight.htb.	604800 IN A	10.10.3.12
internal.inlanefreight.htb. 604800 IN	SOA	inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 3 msec
;; SERVER: 10.129.160.95#53(10.129.160.95) (TCP)
;; WHEN: Mon Jul 24 16:53:22 BST 2023
;; XFR size: 12 records (messages 1, bytes 435)
```

- [ ] and find domain ,IP and A records...

---

### Skill Assessment
- [ ] https://crt.sh/?q=githubapp.com
