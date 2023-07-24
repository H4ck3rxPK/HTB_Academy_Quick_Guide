## <span style=color:red>**Passive Information Gathering**</span>
### [<span style=color:blue>**WHOIS**<SMALL>(TCP/43)</SMALL></span>](https://datatracker.ietf.org/doc/html/rfc3912)
#### Linux
```go!
H4ck3rxPK@htb[/htb]$ export TARGET="facebook.com" # Assign our target to an environment variable
H4ck3rxPK@htb[/htb]$ whois $TARGET
```

#### Windows Sysinternals
```go!
C:\htb> whois.exe facebook.com
```

### **DNS**<SMALL>(UDP or TCP/53)</SMALL>

#### A Records
```go!
H4ck3rxPK@htb[/htb]$ export TARGET="facebook.com"
H4ck3rxPK@htb[/htb]$ nslookup $TARGE

Server:		1.1.1.1
Address:	1.1.1.1#53

Non-authoritative answer:
www.facebook.com	canonical name = star-mini.c10r.facebook.com.
Name:	star-mini.c10r.facebook.com
Address: 31.13.92.36
```
## <span style=color:red>**Active Infrastructure Identification**</span>
### **curl**
```go!
curl -I facebook.com

HTTP/1.1 301 Moved Permanently
Location: https://facebook.com/
Content-Type: text/plain
Server: proxygen-bolt
Date: Mon, 24 Jul 2023 01:42:24 GMT
Connection: keep-alive
Content-Length: 0
```

### **whatweb**
```go!
sudo whatweb -a3 -v app.inlanefreight.local
```
Domain can replace to IP.

### **nslookup**
```go!
```

