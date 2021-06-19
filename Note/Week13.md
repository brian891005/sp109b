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

##### blocking.c

* 會等待使用者輸入字元---->再顯示在螢幕上
```
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>
#include<stdio.h>
#include<stdlib.h>

int main(int argc, char* argv[])
{
    int fd = open("/dev/tty",O_RDONLY); //Open the standard I / O file, which is blocked.
    if(fd == -1){
        perror("open /dev/tty");
        exit(1);
    }
    int ret = 0;
    char buf[1024] = {0};
    while(1){
        ret = read(fd, buf, sizeof(buf));
        if(ret == -1){
            perror("read");
            exit(1);
        }
        else if(ret == 0)
            printf("buf is null\n");
        else if(ret > 0)
            printf("buf is %s\n",buf);
        printf("test\n");
        sleep(1);
    }
    close(fd);

    return 0;
}
```

* 結果
```
ubuntu@foo1:~/sp/08-posix/07-nonblocking$ gcc blocking1.c -o blocking1
ubuntu@foo1:~/sp/08-posix/07-nonblocking$ ./blocking1
-------------------------------等待指令回復---------------------------------------
sda                                                 
buf is sda

test
```

#### nonblocking.c
* 不等待使用者回復便執行程式
```
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>
#include<stdio.h>
#include<stdlib.h>

int main(int argc, char* argv[])
{
    int fd = open("/dev/tty", O_RDONLY | O_NONBLOCK); // O ﹣ Nonblock set file input and output to non blocking
    if(fd == -1){
        perror("open /dev/tty");
        exit(1);
    }
    int ret = 0;
    char buf[1024] = {0};
    while(1){
        ret = read(fd, buf, sizeof(buf));
        if(ret == -1){
            perror("read /dev/tty"); // fputs(stderr, "read /dev/tty")
            printf("no input,buf is null\n");
        }
        else {
            printf("ret = %d, buf is %s\n",ret, buf);
        }
        sleep(1);
    }
    close(fd);

    return 0;
}
```

* 結果
* 不等使用者輸入便重新執行迴圈
```
ubuntu@foo1:~/sp/08-posix/07-nonblocking$ gcc nonblocking1.c -o nonblocking1
ubuntu@foo1:~/sp/08-posix/07-nonblocking$ ./nonblocking1
read /dev/tty: Resource temporarily unavailable
no input,buf is null
read /dev/tty: Resource temporarily unavailable
no input,buf is null
read /dev/tty: Resource temporarily unavailable
no input,buf is null
sdadread /dev/tty: Resource temporarily unavailable
no input,buf is null

asdret = 5, buf is sdad


ret = 4, buf is asd


sad
ret = 4, buf is sad


read /dev/tty: Resource temporarily unavailable
no input,buf is null
read /dev/tty: Resource temporarily unavailable
no input,buf is null
read /dev/tty: Resource temporarily unavailable
no input,buf is null
sad
ret = 4, buf is sad


read /dev/tty: Resource temporarily unavailable
no input,buf is null
dread /dev/tty: Resource temporarily unavailable
no input,buf is null
s
ret = 3, buf is ds



read /dev/tty: Resource temporarily unavailable
no input,buf is null
s
ret = 2, buf is s
```

#### timeTcp
1. make
    * gcc -std=c99 -O0 server.c -o server
    * gcc -std=c99 -O0 client.c -o client
2. ./server& ----> 背景執行
3. ./client -----> 取回時間
* client
```
#include <sys/socket.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <netdb.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <arpa/inet.h>
#include <time.h>
#include <assert.h>
#include <sys/wait.h>

#define PORT 8080

int main(int argc, char *argv[]) {
  struct sockaddr_in serv_addr;
  memset(&serv_addr, 0, sizeof(serv_addr));
  serv_addr.sin_family = AF_INET;
  serv_addr.sin_port = htons(PORT);
  assert(inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr) > 0);

  int sockfd = socket(AF_INET, SOCK_STREAM, 0);
  assert(sockfd >=0);
  assert(connect(sockfd, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) >= 0);
  char recvBuff[1024];
  int n;
  while ((n = read(sockfd, recvBuff, sizeof(recvBuff)-1)) > 0) {
    recvBuff[n] = 0;
    fputs(recvBuff, stdout);
  }
  return 0;
}
```

* server.c
```
#include <sys/socket.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <netdb.h>
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <errno.h>
#include <arpa/inet.h>
#include <time.h>
#include <assert.h>
#include <sys/wait.h>

#define PORT 8080

int main(int argc, char *argv[]) {
  int listenfd = socket(AF_INET, SOCK_STREAM, 0);
  assert(listenfd >= 0);
  struct sockaddr_in serv_addr;
  memset(&serv_addr, 0, sizeof(serv_addr));
  serv_addr.sin_family = AF_INET;
  serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
  serv_addr.sin_port = htons(PORT);

  assert(bind(listenfd, (struct sockaddr*)&serv_addr, sizeof(serv_addr)) >= 0);
  assert(listen(listenfd, 10) >= 0); // 最多十個同時連線
  char sendBuff[1025];
  memset(sendBuff, 0, sizeof(sendBuff));
  while(1) {
    int connfd = accept(listenfd, (struct sockaddr*)NULL, NULL);
    assert(connfd >= 0);
    time_t ticks = time(NULL);
    snprintf(sendBuff, sizeof(sendBuff), "%.24s\r\n", ctime(&ticks));
    assert(write(connfd, sendBuff, strlen(sendBuff)) >=0);
    close(connfd);
    sleep(1);
  }
}

```

* 程式解說
    * 客戶端從伺服器端取得時間
* 結果
```
ubuntu@foo1:~/sp/08-posix/06-net/01-timeTcp1$ make
gcc -std=c99 -O0 server.c -o server
ubuntu@foo1:~/sp/08-posix/06-net/01-timeTcp1$ ./server&
[1] 18029
ubuntu@foo1:~/sp/08-posix/06-net/01-timeTcp1$ ./client
Sat Jun 19 20:29:55 2021
ubuntu@foo1:~/sp/08-posix/06-net/01-timeTcp1$ ./client
Sat Jun 19 20:30:04 2021
```