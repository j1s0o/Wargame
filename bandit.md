# Bandit

## Level 0

ssh bandit0@bandit.labs.overthewire.org -p 2220&#x20;

username : bandit0

password : bandit0

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

`cat readme` để lấy password

**password : NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL**

## &#x20;Level 1

để đọc đươc password trong file - ta có thể sử dụng lệnh `cat ./-`

[source](https://stackoverflow.com/questions/42187323/how-to-open-a-dashed-filename-using-terminal)&#x20;

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

**pasword : rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi**

## Level 2

Ở level này ta dùng \ để cmd hiểu ký tự sau là space nên ta đọc password theo câu lệnh&#x20;

\=> `cat spaces\ in\ this\ filename`

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**password : aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG**

## Level 3

Ở level này ta sẽ đọc password trong 1 file ẩn&#x20;

Đầu tiên ta `cd inhere` sau đó `ls -la` để xem có file ẩn không và thấy .hidden sau đó ta chỉ việc `cat .hidden` thôi

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**password : 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe**

## **Level 4**

Ở level này đề nói password nằm trong 1 file có thể đọc được nên ta sẽ làm như sau

Đầu tiên ta sẽ vào thư mục inhere `cd inhere` sau đó dùng lệnh file để kiểm tra tất cả file => `file ./-f*`

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Ta sẽ thất -file07 là 1 file ASCII text nên ta sẽ đọc file đó

**password : lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR**

## Level 5

Đề gợi ý cho ta là password nằm ở file có size 1033c nên ta sẽ dùng lệnh `find -type f -size 1033c` với **-type f** để tìm tất cả file và **-size 1033c** để tìm tất cả file có size 1033c

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**password : P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU**

## Level 6

Level này đề cho user là bandit7 , group bandit6 và size 33 nên ta sẽ sử dụng lệnh&#x20;

find / -user bandit7 -group bandit6 -size 33c&#x20;

Nhưng ở đây ta sẽ bị rất nhiều Permission denied&#x20;

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Nên ta sẽ thêm vào câu lệnh 2>/dev/null ([source](https://www.cyberciti.biz/faq/bash-find-exclude-all-permission-denied-messages/))

\=> find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**password : z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S**

## Level 7
