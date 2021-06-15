## 生產者與消費者問題

也稱有限緩衝問題（Bounded-buffer problem），是一個多進程同步問題的經典案例。該問題描述了共享固定大小緩衝區的兩個進程——即所謂的「生產者」和「消費者」——在實際運行時會發生的問題。生產者的主要作用是生成一定量的數據放到緩衝區中，然後重複此過程。與此同時，消費者也在緩衝區消耗這些數據。該問題的關鍵就是要保證生產者不會在緩衝區滿時加入數據，消費者也不會在緩衝區中空時消耗數據。

要解決該問題，就必須讓生產者在緩衝區滿時休眠（要麼乾脆就放棄數據），等到下次消費者消耗緩衝區中的數據的時候，生產者才能被喚醒，開始往緩衝區添加數據。同樣，也可以讓消費者在緩衝區空時進入休眠，等到生產者往緩衝區添加數據之後，再喚醒消費者。通常採用進程間通信的方法解決該問題，常用的方法有信號燈法[1]等。如果解決方法不夠完善，則容易出現死鎖的情況。出現死鎖時，兩個執行緒都會陷入休眠，等待對方喚醒自己。該問題也能被推廣到多個生產者和消費者的情形。


* 使用語法---->信號量(signal()&wait())

* 定義 
是一個同步物件，用於保持在0至指定最大值之間的一個計數值。當執行緒完成一次對該semaphore物件的等待（wait）時，該計數值減一；當執行緒完成一次對semaphore物件的釋放（release）時，計數值加一。當計數值為0，則執行緒等待該semaphore物件不再能成功直至該semaphore物件變成signaled狀態。semaphore物件的計數值大於0，為signaled狀態；計數值等於0，為nonsignaled狀態.

* 語法
計數號誌具備兩種操作動作，稱為V（signal()）與P（wait()）（即部分參考書常稱的「PV操作」）。V操作會增加訊號標S的數值，P操作會減少它。

運作方式：

1. 初始化，給與它一個非負數的整數值。
2. 執行P（wait()），訊號標S的值將被減少。企圖進入臨界區段的行程，需要先執行P（wait()）。當訊號標S減為負值時，行程會被擋住，不能繼續；當訊號標S不為負值時，行程可以獲准進入臨界區段。
3. 執行V（signal()），訊號標S的值會被增加。結束離開臨界區段的行程，將會執行V（signal()）。當訊號標S不為負值時，先前被擋住的其他行程，將可獲准進入臨界區段。


* 程式碼(非完整)
```
int itemCount = 0;

procedure producer() {
    while (true) {
        item = produceItem();
        if (itemCount == BUFFER_SIZE) {
            sleep();
        }
        putItemIntoBuffer(item);
        itemCount = itemCount + 1;
        if (itemCount == 1) {
            wakeup(consumer);
        }
    }
}

procedure consumer() {
    while (true) {
        if (itemCount == 0) {
            sleep();
        }
        item = removeItemFromBuffer();
        itemCount = itemCount - 1;
        if (itemCount == BUFFER_SIZE - 1) {
            wakeup(producer);
        }
        consumeItem(item);
    }
}
```
* 上面代碼中的問題在於它可能導致競爭條件，進而引發死鎖。考慮下面的情形：

1. 消費者把最後一個 itemCount 的內容讀出來，注意它現在是零。消費者返回到while的起始處，現在進入 if 塊；
2. 就在調用sleep之前，CPU決定將時間讓給生產者，於是消費者在執行 sleep 之前就被中斷了，生產者開始執行；
3. 生產者生產出一項數據後將其放入緩衝區，然後在 itemCount 上加 1；
4. 由於緩衝區在上一步加 1 之前為空，生產者嘗試喚醒消費者；
5. 遺憾的是，消費者並沒有在休眠，喚醒指令不起作用。當消費者恢復執行的時候，執行 sleep，一覺不醒。出現這種情況的原因在於，消費者只能被生產者在 itemCount 為 1 的情況下喚醒；
6. 生產者不停地循環執行，直到緩衝區滿，隨後進入休眠。

由於兩個進程都進入了永遠的休眠，死鎖情況出現了。因此，該算法是不完善的。

## 哲學家就餐問題
這個問題旨在說明避免死結的挑戰，死結是一種程式無法繼續執行的狀態

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/philospher.jpg)

* 規則

哲學家就餐問題可以這樣表述，假設有五位哲學家圍坐在一張圓形餐桌旁，做以下兩件事情之一：吃飯，或者思考。吃東西的時候，他們就停止思考，思考的時候也停止吃東西。餐桌中間有一大碗義大利麵，每位哲學家之間各有一支餐叉。因為用一支餐叉很難吃到義大利麵，所以假設哲學家必須用兩支餐叉吃東西。他們只能使用自己左右手邊的那兩支餐叉。哲學家就餐問題有時也用米飯和五根筷子而不是義大利麵和餐叉來描述，因為吃米飯必須用兩根筷子。

這個問題不考慮義大利麵有多少，也不考慮哲學家的胃有多大。假設兩者都是無限大。

問題在於如何設計一套規則，使得在哲學家們在完全不交談，也就是無法知道其他人可能在什麼時候要吃飯或者思考的情況下，可以在這兩種狀態下永遠交替下去。

* 解法

1. 服務生解法
一個簡單的解法是引入一個餐廳服務生，哲學家必須經過他的允許才能拿起餐叉。因為服務生知道哪支餐叉正在使用，所以他能夠作出判斷避免死結。

為了演示這種解法，假設哲學家依次標號為A至E。如果A和C在吃東西，則有四支餐叉在使用中。B坐在A和C之間，所以兩支餐叉都無法使用，而D和E之間有一隻空餘的餐叉。假設這時D想要吃東西。如果他拿起了第五支餐叉，就有可能發生死結。相反，如果他徵求服務生同意，服務生會讓他等待。這樣，我們就能保證下次當兩把餐叉空餘出來時，一定有一位哲學家可以成功的得到一對餐叉，從而避免了死結。

2. 資源分級解法
另一個簡單的解法是為資源（這裡是餐叉）分配一個偏序或者分級的關係，並約定所有資源都按照這種順序取得，按相反順序釋放，而且保證不會有兩個無關資源同時被同一項工作所需要。在哲學家就餐問題中，資源（餐叉）按照某種規則編號為1至5，每一個工作單元（哲學家）總是先拿起左右兩邊編號較低的餐叉，再拿編號較高的。用完餐叉後，他總是先放下編號較高的餐叉，再放下編號較低的。在這種情況下，當四位哲學家同時拿起他們手邊編號較低的餐叉時，只有編號最高的餐叉留在桌上，從而第五位哲學家就不能使用任何一支餐叉了。而且，只有一位哲學家能使用最高編號的餐叉，所以他能使用兩支餐叉用餐。當他吃完後，他會先放下編號最高的餐叉，再放下編號較低的餐叉，從而讓另一位哲學家拿起後邊的這隻開始吃東西。

3. Chandy/Misra解法
1984年，曼尼·錢迪和賈亞達夫·米斯拉提出了哲學家就餐問題的另一個解法[5] ，允許任意的使用者（編號{\displaystyle P_{1},\cdots ,P_{n}}{\displaystyle P_{1},\cdots ,P_{n}}）爭用任意數量的資源。與資源分級解法不同的是，這裡編號可以是任意的。

* 把餐叉湊成對，讓要吃的人先吃，沒餐叉的人得到一張換餐叉券。
* 餓了，把換餐叉券交給有餐叉的人，有餐叉的人吃飽了會把餐叉交給有券的人。有了券的人不會再得到第二張券。
* 保證有餐叉的都有得吃。

這個解法允許很大的並列性，適用於任意大的問題。

* 程式碼
```
// 本程式修改自 -- https://wwwhome.ewi.utwente.nl/~pieter/CS-OS/Philosophers.c
/*  Philosophers.c - Demonstrate the semaphore solution to the dining
    philosophers problem. The Room semaphore is neede to prevent all
    philosophers from entering the room and grabbing their left most fork
    at the same time, which would lead to deadlock. The Room sempahore
    can be enabled by specifying a command line argument.  */

#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
typedef	enum { False=0, True=1 } bool ;

#define N 5 /* Number of times each philosopher tries to eat */
#define P 3 /* Number of philosophers */

sem_t Room;
sem_t Fork[P];
bool Switch ;

void *tphilosopher(void *ptr) {
    int i, k = *((int *) ptr);
    for(i = 1; i <= N; i++) {
        printf("%*cThink %d %d\n", k*4, ' ', k, i);
        if(Switch) {
            sem_wait(&Room) ;
        }
        sem_wait(&Fork[k]) ;
        sem_wait(&Fork[(k+1) % P]) ;
        printf("%*cEat %d %d\n", k*4, ' ', k, i);
        sem_post(&Fork[k]) ;
        sem_post(&Fork[(k+1) % P]) ;
        if(Switch) {
            sem_post(&Room) ;
        }
    }
    pthread_exit(0);
}

int main(int argc, char * argv[]) {
    int i, targ[P];
    pthread_t thread[P];
    sem_init(&Room, 0, P-1);    
    Switch = (argc > 1); /* Room semaphore on/off */
    printf("Switch=%s\n",(Switch?"true":"false"));
    for(i=0;i<P;i++) {
        sem_init(&Fork[i], 0, 1);    
    }
    for(i=0;i<P;i++) {
        targ[i] = i;
        pthread_create(&thread[i], NULL, &tphilosopher,(void *) &targ[i]);
    }
    for(i=0;i<P;i++) {
        pthread_join(thread[i], NULL);
    }
    for(i=0;i<P;i++) {
        sem_destroy(&Fork[i]);
    }
    sem_destroy(&Room);
    return 0;
}

/*  Please note that the checks on the return value of the system calls
    have been omitted to avoid cluttering the code. However, system calls
    can and will fail, in which case the results are unpredictable. */
```
* 執行結果
```
ubuntu@foo1:~/sp/08-posix/02-thread$ ./philospher
Switch=false
        Think 2 1
        Eat 2 1
        Think 2 2
        Eat 2 2
        Think 2 3
        Eat 2 3
        Think 2 4
        Eat 2 4
        Think 2 5
        Eat 2 5
    Think 1 1
    Eat 1 1
    Think 1 2
    Eat 1 2
    Think 1 3
    Eat 1 3
    Think 1 4
    Eat 1 4
    Think 1 5
    Eat 1 5
 Think 0 1
 Eat 0 1
 Think 0 2
 Eat 0 2
 Think 0 3
 Eat 0 3
 Think 0 4
 Eat 0 4
 Think 0 5
 Eat 0 5
```


