
---

These are directions for using ffuf to practice this method. Tools such as gobuster, dirb, and dirsearch work similarly. For example:
* gobuster dir -u http://example.htb/ -w /usr/share/wordlists/dirb/common.txt
*  dirb http://example.htb/

---
```go!
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

"FFuF wordlist" --> https://github.com/danielmiessler/SecLists

H4ck3rxPK123@htb[/htb]$ git clone https://github.com/danielmiessler/SecLists

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

## <span style=color:red>**Basic Fuzzing**</span>
### Directory
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

### Extension
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/indexFUZZ

.phps            [Status: 403, Size: 281, Words: 20, Lines: 10, Duration: 0ms]
.php             [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 0ms]
```
"indexFUZZ" instead of "index.FUZZ" --> also other potentially hidden or backup files that might not follow the typical pattern of having a dot before the extension.  

### Page
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://94.237.56.76:43557/blog/FUZZ.php
```

### Recursive
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

## <span style=color:red>**Domain Fuzzing**</span>
```go!
sudo sh -c 'echo "SERVER_IP  DOMAIN_NAME" >> /etc/hosts'
```
### Sub-domain
```go!
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.hackthebox.eu
```
```go!
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.IP:PORT
```

### vHost
```go!
ffuf -w /opt/useful/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:36402/ -H 'Host: FUZZ.academy.htb' -ms 0
```
```go
| Options  | Description                                                         |
| -------- | ------------------------------------------------------------------- |
| -ms      | match the response size                                             |
```

## <span style=color:red>**Parameter Fuzzing**</span>
### <span style=color:blue>**GET**</span>
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key
```

### <span style=color:blue>**POST**</span>
```go!
H4ck3rxPK@htb[/htb]$ ffuf -w /opt/useful/SecLists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs 774
```
```go
| Options  | Description                                                         |
| -------- | ------------------------------------------------------------------- |
| -fs      | Filter HTTP response size. i.e., -fs 774, then filter all 774 size  |
```

### Value

#### Find the Looking for the appropriate wordlist or custom wordlist to crack potential parameter values.

