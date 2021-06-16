## 實作

#### io1.c
```
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#define SMAX 128

int main() {
  int a = open("a.txt", O_RDWR); //傳回a的檔案描述值
  int b = open("b.txt", O_CREAT|O_RDWR, 0644);//傳回a的檔案描述值
  char text[SMAX];
  int n = read(a, text, SMAX); //讀檔內文字並存入text
  printf("n=%d\n", n);
  write(b, text, n);  //寫入b.txt
  printf("a=%d, b=%d\n", a, b);
}

```

* 結果
讀檔/寫檔
```
ubuntu@foo1:~/sp/08-posix/04-fs/00-basic$ ./io1                 
n=10
a=3, b=4
----------b檔的內容------------
ubuntu@foo1:~/sp/08-posix/04-fs/00-basic$ cat b.txt             
hello!
hi
```