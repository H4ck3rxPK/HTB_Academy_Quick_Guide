--- 
- whois ---> query the ownership information of a domain name or IP address. 

- whatweb --->  fingerprinting tool. It can identify the technologies used by websites(server software, web content management systems(CMS), JavaScript libraries, tracking and analytics codes, etc.)

- nslookup, dig --->DNS query tool.
---
```go!
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

"FFuF wordlist" --> https://github.com/danielmiessler/SecLists

In PwnBox, entire SecLists repo available under /opt/useful/SecLists.

Utilizing for pages and directory fuzzing is another commonly used wordlist called "directory-list-2.3".
```

```go!
| HTTP Status Code | Brief Description  | HTTP Status Code | Brief Description  |
| ---------------- | ------------------ | ---------------- | ------------------ |
|       200        |         OK         |       307        | Temporary Redirect |
| ---------------- | ------------------ | ---------------- | ------------------ |
|       204        |     No Content     |       401        |    Unauthorized    |
| ---------------- | ------------------ | ---------------- | ------------------ |
|       301        | Moved Permanently  |       403        |     Forbidden      |
| ---------------- | ------------------ | ---------------- | ------------------ |
|       302        |       Found        |

```

## <span style=color:red>Basic Fuzzing</span>
### Directory Fuzzing
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txtl:FUZZ -u http://SERVER_IP:PORT/FUZZ

forum           [Status: 301, Size: 323, Words: 20, Lines: 10, Duration: 0ms]
```
```go!
H4ck3rxPK@htb[/htb]$ curl -I http://SERVER_IP:PORT/forum

HTTP/1.1 301 Moved Permanently
Date: Wed, 26 Jul 2023 01:57:15 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: http://94.237.62.195:52616/forum/
Content-Type: text/html; charset=iso-8859-1
```

### Extension Fuzzing
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ

.phps            [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 0ms]
.php             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
```
"indexFUZZ" instead of "index.FUZZ" --> also other potentially hidden or backup files that might not follow the typical pattern of having a dot before the extension.  

### Page Fuzzing
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.56.76:43557/blog/FUZZ.php
```

### Recursive Fuzzing
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.56.76:43557/FUZZ -recursion -recursion-depth 1 -e .php -v

index.php          [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 0ms]
blog               [Status: 301, Size: 320, Words: 20, Lines: 10, Duration: 0ms]
[INFO] Adding a new job to the queue: http://94.237.56.76:34121/blog/FUZZ

forum              [Status: 301, Size: 321, Words: 20, Lines: 10, Duration: 0ms]
[INFO] Adding a new job to the queue: http://94.237.56.76:34121/forum/FUZZ

.php               [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 0ms]
                   [Status: 200, Size: 986, Words: 423, Lines: 56, Duration: 3ms]
[INFO] Starting queued job on target: http://94.237.56.76:34121/blog/FUZZ

index.php          [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
home.php           [Status: 200, Size: 1046, Words: 438, Lines: 58, Duration: 1ms]
                   [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
.php               [Status: 403, Size: 280, Words: 20, Lines: 10, Duration: 0ms]
[INFO] Starting queued job on target: http://94.237.56.76:34121/forum/FUZZ

index.php          [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
flag.php           [Status: 200, Size: 21, Words: 1, Lines: 1, Duration: 2ms]
```
Bingo!

## <span style=color:red>Domain Fuzzing</span>
```go!
H4ck3rxPK@htb[/htb]$ sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'
```
### Sub-domain Fuzzing
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u http://FUZZ.hackthebox.eu
```

### 
