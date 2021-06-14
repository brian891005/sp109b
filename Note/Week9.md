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