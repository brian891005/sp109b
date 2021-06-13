# 函式庫與thread

## UNIX
一種多使用者、多行程的電腦作業系統，源自於從20世紀70年代開始在美國AT&T公司的貝爾實驗室開發的AT&T Unix

* 演進
Unix的前身為1964年開始的Multics，1965年時，貝爾實驗室加入一項由奇異和麻省理工學院合作的計劃；該計劃要建立一套多使用者、多工、多層次（multi－user、multi－processor、multi－level）的MULTICS作業系統。貝爾實驗室參與了這個作業系統的研發，但因為開發速度太慢，1969年貝爾實驗室決定退出這個計劃。貝爾實驗室的工程師，肯·湯普遜和丹尼斯·里奇，在此時自行開發了Unix。

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/UNIX.jpg)

## vim的使用
* http://kevinchen.synology.me/TechnicalDocuments/About_Linux/Fread_eazyvim.html

1. 只要沒事就按 Esc，讓他離開編輯模式，就可以輸入指令。若想進入編輯模式就按一下 i ，就可以開始編輯及插入文字。
2. 然後只要學會在非編輯模式下輸入 :w 與 :q 再加上 Enter，就會存檔和離開程式。
3. yy 和 p 超好用，講明白了就是複製一行與貼上，剪下一行則是用 cc。
    * 複製三行用 3yy
4. 像要插入一行空白行，並直接進入編輯模式在新的一行開始編輯，就用 o。
5. 刪除一行用 dd。 (刪除三行用 3dd)
6. 跳到指定第幾行就這樣用 :100 （到第一百行）
7. 尋找字串用 / ，如： /fred。取代所有字串用 :%s/fred/pig/g 。
8. u 要配合 Ctrl+r 一起用，一個是復原，一個是取消復原。不過 Ctrl+r 沒痛過的話，比較難記得，要經歷過很多次的誤按 u ，才會痛定思痛把這個組合鍵背起來。XD


基本上，這樣就夠了，等用熟了再來學更多吧。（什麼 Shift+V 之類的東西） 喔，對了，注意指令的大小寫。請確定 Caps Lock 的燈是滅的再來按指令。


* 剪貼 (Cut and paste:)

開頭按 v, 結尾按 d (剪下) 或 y (複製)

最後按 p 貼上！

1. Position the cursor where you want to begin cutting.
2. Press v to select characters (or uppercase V to select whole lines).
3. Move the cursor to the end of what you want to cut.
4. Press d to cut (or y to copy).
5. Move to where you would like to paste.
6. Press P to paste before the cursor, or p to paste after.

* 參考老師的vim.md

## 連結外部函式庫

* 先下載標準外部函式庫檔
* 載入函式庫，同時編譯
* 例子(glist.c)
```
/* Compile with:
export CFLAGS="`pkg-config --cflags glib-2.0` -g -Wall -std=gnu11 -O3"
export LDLIBS="`pkg-config --libs   glib-2.0`"
make glist
*/
#include <stdio.h>
#include <glib.h>

GList *list;

int main(){
    list = g_list_append(list, "a");
    list = g_list_append(list, "b");
    list = g_list_append(list, "c");
    printf("The list is now %d items long\n", g_list_length(list));

    for ( ; list!= NULL; list=list->next)
        printf("%s\n", (char*)list->data);

    printf("The list is now %d items long\n", g_list_length(list));
}
```

* 步驟(環境linux)
1. 下載sudo apt-get install libglib2.0-dev
2. 執行編譯gcc glist.c -o glist `pkg-config --cflags glib-2.0` -g -Wall -std=gnu11 -O3 `pkg-config --libs glib-2.0` 
3. 與外部函式庫連結
4. 執行檔案./glist

* 執行結果
```
ubuntu@foo1:~/sp/07-lib/03-glib2$ ./glist
The list is now 3 items long
a
b
c
The list is now 0 items long
```