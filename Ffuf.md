```go!
        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

'wordlist' https://github.com/danielmiessler/SecLists

In PwnBox, entire SecLists repo available under /opt/useful/SecLists.

Utilizing for pages and directory fuzzing is another commonly used wordlist called "directory-list-2.3".
```

```go!
| HTTP Status Code | Brief Description  |
| ---------------- | ------------------ |
|       200        |         OK         |
| ---------------- | ------------------ |
|       204        |     No Content     |
| ---------------- | ------------------ |
|       301        | Moved Permanently  |
| ---------------- | ------------------ |
|       302        |       Found        |
| ---------------- | ------------------ |
|       307        | Temporary Redirect |
| ---------------- | ------------------ |
|       401        |    Unauthorized    |
| ---------------- | ------------------ |
|       403        |     Forbidden      |
```

## <span style=color:red>Basic Fuzzing</span>
### Directory Fuzzing
```go!
sudo ffuf -w /opt/useful/SecLists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://SERVER_IP:PORT/FUZZ
```