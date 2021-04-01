# 編譯器介紹
編譯器是用來將高階語言轉換成組合語言 (或者是機器碼) 的工具程式。有了編譯器或直譯器，程式設計師才能用高階語言撰寫程式。因此，編譯器是程式設計師的重要工具，也是系統程式課程的重點之一。

# 編譯器的六大階段
![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/compiler.jpg)
* 參考陳忠誠老師的系統程式課程圖

# 指標
變數提供具名稱的記憶體儲存空間，一個變數關聯一個資料型態、儲存的值與儲存空間的位址值。指標則是指出變數位置的所在，利用* 的方式來指向所儲存的記憶體位址。

* 指標例子
```
#include <stdio.h>

int main(void) {
    int n = 10;
    int *p = &n ;

    printf("n 的位址：%p\n", &n);
    printf("p 儲存的位址：%p\n", p);

    return 0;
}
```
* 執行結果
```
n 的位址：0061FEC8      
p 儲存的位址：0061FEC8  
```
* 從p來看
```
#include <stdio.h>

int main(void) {
    int n = 10;
    int *p = &n;

    printf("指標 p 儲存的值：%p\n", p);
    printf("取出 p 儲存位址處之值：%d\n", *p);

    return 0;
}
```
* 執行結果
```
指標 p 儲存的值：0061FEC8
取出 p 儲存位址處之值：10
```
### 指標與陣列
在宣告陣列之後，使用到陣列變數時，會取得首元素的位址。陣列索引其實是相對於首元素位址的位移量，下面這個程式以指標運算與陣列索引操作，顯示出相同的對應位址值：
```
#include <stdio.h>
#define LEN 10

int main(void) {
    int arr[LEN] = {0};
    int *p = arr;

    for(int i = 0; i < LEN; i++) {
        printf("&arr[%d]: %p", i ,&arr[i]);
        printf("\t\tptr + %d: %p\n", i, p + i);
    }

    return 0;
}
```
* 顯示結果
```
&arr[0]: 0061FEA0               ptr + 0: 0061FEA0
&arr[1]: 0061FEA4               ptr + 1: 0061FEA4
&arr[2]: 0061FEA8               ptr + 2: 0061FEA8
&arr[3]: 0061FEAC               ptr + 3: 0061FEAC
&arr[4]: 0061FEB0               ptr + 4: 0061FEB0
&arr[5]: 0061FEB4               ptr + 5: 0061FEB4
&arr[6]: 0061FEB8               ptr + 6: 0061FEB8
&arr[7]: 0061FEBC               ptr + 7: 0061FEBC
&arr[8]: 0061FEC0               ptr + 8: 0061FEC0
&arr[9]: 0061FEC4               ptr + 9: 0061FEC4
```

### 野指標
如果宣告指標但不指定初值，則指標儲存的位址是未知的，存取未知位址的記憶體內容是危險的。會造成不可預知的結果(隨機指向)，最好為指標設定初值，如果指標一開始不儲存任何位址，可設定初值為 0。

* 例子
```
int *p; 
*p = 10;
```
### fread()參數用法
```
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *stream)
```
* ptr -- 这是指向带有最小尺寸 size*nmemb 字节的内存块的指针。
* size -- 这是要读取的每个元素的大小，以字节为单位。
* nmemb -- 这是元素的个数，每个元素的大小为 size 字节。
* stream -- 这是指向 FILE 对象的指针，该 FILE 对象指定了一个输入流。