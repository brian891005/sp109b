## 作業系統
* 演進

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/作業系統.jpg)

* 五大功能模組
    * 行程管理
    * 記憶體管理
    * 輸出入管理
    * 檔案管理
    * 使用者介面

## 單行程與多工系統

* 多工
同時執行多個程式

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/單行程與多工.jpg)

* 範例(共用變數) race.c
    * gcc race.c -o race -lpthread
    * ./race
    * race condition(競爭情況模擬)--->組合語言不照順序跑
    * 改善方法--->pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER初始化--->透過pthread_mutex_lock和pthread_mutex_unlock解決競爭情況(共用變數問題)
```
#include <stdio.h>
#include <pthread.h>

#define LOOPS 100000000
int counter = 0;

void *inc()
{
  for (int i=0; i<LOOPS; i++) {
    counter = counter + 1;
  }
  return NULL;
}

void *dec()
{
  for (int i=0; i<LOOPS; i++) {
    counter = counter - 1;
  }
  return NULL;
}


int main() 
{
  pthread_t thread1, thread2;

  pthread_create(&thread1, NULL, inc, NULL);
  pthread_create(&thread2, NULL, dec, NULL);

  pthread_join( thread1, NULL);  //等此程式跑完才會跑主程式
  pthread_join( thread2, NULL);  
  printf("counter=%d\n", counter);
}
```
* 執行結果
每次執行不一樣---->發生race condition
```
ubuntu@foo1:~/sp/08-posix/02-thread$ gcc race.c -o race -lpthread
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=1531380
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=1310381
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=2161771
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=0
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=-4438851
ubuntu@foo1:~/sp/08-posix/02-thread$ ./race
counter=-4378475
```

* norace.c---->add pthread_mutex_lock和pthread_mutex_unlock
    * gcc norace.c -o norace -lpthread
    * ./norace
    * 結果conter永遠保持0
* 程式碼
```
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t mutex1 = PTHREAD_MUTEX_INITIALIZER;
#define LOOPS 100000
int counter = 0;

void *inc()
{
  for (int i=0; i<LOOPS; i++) {
    pthread_mutex_lock( &mutex1 ); //上鎖，避免競爭情況
    counter = counter + 1;          //修改共用變數
    pthread_mutex_unlock( &mutex1 ); //變數修改完畢，解鎖
  }
  return NULL;
}

void *dec()
{
  for (int i=0; i<LOOPS; i++) {
    pthread_mutex_lock( &mutex1 );//上鎖，避免競爭情況
    counter = counter - 1;          //修改共用變數
    pthread_mutex_unlock( &mutex1 );//變數修改完畢，解鎖
  }
  return NULL;
}


int main() 
{
	pthread_t thread1, thread2;

	pthread_create(&thread1, NULL, inc, NULL);
  pthread_create(&thread2, NULL, dec, NULL);

  pthread_join( thread1, NULL);
  pthread_join( thread2, NULL);
  printf("counter=%d\n", counter);
}
```

* 當lock不好會出現死結
* 示意圖

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/deadlock.jpg)

## 協同式多工

* 當一個程式當機會導致整個系統失效
* 偽多工系統 

## 行程行為模式

* 動作可分兩種
    * 使用CPU------------------->進行計算工作
    * 使用I/O(input/output)----->進行輸出入動作
* 作業系統利用輸出入空檔切換行程

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/process_cpu_io.jpg)

* 利用中斷機制來避免當機
    * 處理狀況--->無窮迴圈
    * 在CPU執行下個行程時，設定中斷時間點
    * 當行程霸佔CPU時，可用中斷機制奪回cpu控制權

* 行程狀態表示圖

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/process_condition.jpg)

## 排程器
### 排程方法

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/process_way.jpg)