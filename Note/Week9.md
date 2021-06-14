## 記憶體配置

以vmem.c做示範
* 程式碼
```
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    printf("location of code : %p\n", main); //主程式在記憶體位置
    printf("location of heap : %p\n", malloc(100e6)); //配置一個 100e6 需要的空間，並傳回該空間的記憶體位址
    int x = 3;
    printf("location of stack: %p\n", &x); //區域變數在記憶體位置
    return 0;
}
```
* 步驟
1. gcc veme.c -o veme ---> 先編譯
2. ./veme
3. 結果
```
ubuntu@foo1:~/sp/08-posix/01-basic$ gcc vmem.c -o vmem
ubuntu@foo1:~/sp/08-posix/01-basic$ ./vmem
location of code : 0x563a6a86d189
location of heap : 0x7f4855b70010
location of stack: 0x7ffc816b14d4
```

## 行程管理系統
打造出一個環境，讓任何程式都能輕易被執行，而不受到其他程式干擾，就好像整台電腦完全接受該程式指揮一樣

### 行程(process)---->工作管理員
是指電腦中已執行的程式。行程曾經是分時系統的基本運作單位。在面向行程設計的系統（如早期的UNIX，Linux 2.4及更早的版本）中，行程是程式的基本執行實體；在面向執行緒設計的系統（如當代多數作業系統、Linux 2.6及更新的版本）中，行程本身不是基本執行單位，而是執行緒的容器。

* 在shell打ps指令--->顯示出現在木情再跑的行程
```
      PID    PPID    PGID     WINPID   TTY         UID    STIME COMMAND
     2349    1994    2349      22840  cons0     197609 03:47:57 /c/Program Files/Multipass/bin/multipass
     2488    2279    2488       4484  cons3     197609 18:54:49 /usr/bin/PS
     1994       1    1994      16300  cons0     197609   Jun 12 /usr/bin/bash
     2005       1    2005      11388  cons1     197609   Jun 12 /usr/bin/bash
     2178       1    2178      12992  cons2     197609   Jun 13 /usr/bin/bash
     2279       1    2279      18424  cons3     197609   Jun 13 /usr/bin/bash
```
* 在執行檔以forver舉例-->./forver &(在背景執行)--->方便觀察
* 只要在shell輸入kill+PID(你要刪除的行程代號)--->刪除該行程

## pthread函式庫
* 以georgeMary.c來看
* 執行步驟
    * gcc georgeMary.c -o georgeMary -lpthread --->-l為的是連結外部函式庫
    * 產生執行碼georgeMary
    * ./georgeMary--->執行
```
#include <pthread.h>     // 引用 pthread 函式庫
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h> 

void *print_george(void *argu) {    // 每隔一秒鐘印出一次 George 的函數
  while (1) {    
    printf("George\n");    
    sleep(1);    
  }    
  return NULL;    
}    

void *print_mary(void *argu) {     // 每隔2秒鐘印出一次 Mary 的函數
  while (1) {    
    printf("Mary\n");    
    sleep(2);    
  }    
  return NULL;    
}    

int main() {     // 主程式開始
  pthread_t thread1, thread2;     // 宣告兩個執行緒
  pthread_create(&thread1, NULL, &print_george, NULL);    // 執行緒 print_george
  pthread_create(&thread2, NULL, &print_mary, NULL);    // 執行緒 print_mary
  while (1) {     // 主程式每隔一秒鐘
    printf("----------------\n");    // 就印出分隔行
    sleep(1);     // 停止一秒鐘
  }    
  return 0;    
}

```
* 執行結果---->迴圈^C跳出
```
ubuntu@foo1:~/sp/08-posix/02-thread$ ./georgeMary
----------------
Mary
George
----------------
George
Mary
----------------
George
----------------
George
Mary
----------------
George
```