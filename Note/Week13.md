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

#### stderr2.c
* stderr (標準錯誤輸出)

標準錯誤輸出是另一輸出串流，用於輸出錯誤訊息或診斷。 它獨立於標準輸出，且可以分別被重導。 常見的目的則為啟始這個程式的終端，即使其標準輸出被重導亦如此。 例如：一個管線中的程式的輸出被重導到下一個程式，但錯誤訊息仍然直接流向文字終端機
* 顯示於終端機
* 程式碼
```
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>

int main() {
  int fdb = open("log.txt", O_CREAT|O_RDWR, 0644);  // 打開檔案 log.txt 並取得代號 fdb
  dup2(fdb, 2); // 複製 fdb 檔案描述子到 2 (stderr)
  fprintf(stderr, "Warning: xxx\n");
  fprintf(stderr, "Error: yyy\n");
}

```

* 結果------>會將輸出的檔案值複製到log.txt
```
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ ./stderr2
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ ls
README.md  error.txt  log.txt  output.txt  stderr1.c  stderr2  stderr2.c
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ error.text
error.text: command not found
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ cat error.text
cat: error.text: No such file or directory
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ cat error.txt
Warning: xxx
Error: yyy
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$ cat log.txt
Warning: xxx
Error: yyy
ubuntu@foo1:~/sp/08-posix/04-fs/04-stderr$
```