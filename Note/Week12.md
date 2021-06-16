## 行程管理

#### fork1.c
* 步驟
1. gcc fork1.c -o fork1
2. ./fork1
```
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h>

int main() { 
    fork(); // 一個行程分叉成父子兩個行程
    fork(); // 兩個行程又分別分叉出兩對父子，所以變成四個行程。
    printf("%-5d : Hello world!\n", getpid());
}

``` 
* 執行結果
```
ubuntu@foo1:~/sp/08-posix/03-fork/01-hello$ 12372 : Hello world! //父行程
12371 : Hello world!                                             //子行程
12373 : Hello world!                                             //父行程
./fork1                                                           
12374 : Hello world!                                             //子行程
```
* fork() -----> 複製行程 ----> 父子行程
* gitpid() ---> 取得行程指令碼

#### fork2.c
* 步驟
1. gcc fork2.c -o fork2
2. ./fork2
```
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h>

int main() { 
    printf("%-5d : enter\n", getpid());
    fork(); // 一個行程分叉成父子兩個行程
    printf("%-5d : after 1st fork\n", getpid());
    fork(); // 兩個行程又分別分叉出兩對父子，所以變成四個行程。
    printf("%-5d : Hello world!\n", getpid());
}
```
* 用程式標示找出父子行程
* 結果
```
ubuntu@foo1:~/sp/08-posix/03-fork/01-hello$ ./fork2
12393 : enter
12393 : after 1st fork
12393 : Hello world!
ubuntu@foo1:~/sp/08-posix/03-fork/01-hello$ 12395 : Hello world!
12394 : after 1st fork
12394 : Hello world!
12396 : Hello world!
```
可以簡單的觀察出父子行程的分別

#### 02-child fork2.c
* 步驟
1. gcc fork2.c -o fork2
2. ./fork2
```
#include <stdio.h> 
#include <sys/types.h> 
#include <unistd.h>

int main() { 
    fork();  // 一個行程分叉成父子兩個行程
    if (fork()==0) { // 兩個行程又分別分叉出兩對父子，所以變成四個行程。
      printf("%-5d: I am child!\n", getpid());
    } else {
      printf("%-5d: I am parent!\n", getpid());
    }
}
```
* 明確標示出父子行程的分別 fork==0時為parent，其他則為child
* 結果
```
ubuntu@foo1:~/sp/08-posix/03-fork/02-child$ ./fork2
12402: I am parent!
ubuntu@foo1:~/sp/08-posix/03-fork/02-child$ 12404: I am child!
12403: I am parent!
12405: I am child!
```

#### execvp1.c(行程替換) ---->顯示檔案行程時間(ls -l)
* 步驟
1. gcc execvp1.c -o execvp1
2. ./execvp1
```
#include <stdio.h>
#include <unistd.h>

int main() {
  char *arg[] = {"ls", "-l", NULL };
  printf("execvp():before\n");
  execvp(arg[0], arg);
  printf("execvp():after\n");
}
```
* 結果
    * 呼叫ls -l
```
ubuntu@foo1:~/sp/08-posix/03-fork/03-exec$ ./execvp1
execvp():before
total 28
drwxrwxr-x 2 ubuntu ubuntu  4096 Mar 29 10:19 backup
-rwxrwxr-x 1 ubuntu ubuntu 16792 Jun 16 21:10 execvp1
-rw-rw-r-- 1 ubuntu ubuntu   176 Mar 29 10:19 execvp1.c
```

#### system1.c ---->是c語言的基本韓式，可編程已編好之程序指令
* 步驟
1. gcc system1.c -o system1
2. ./system1
```
#include <stdio.h>
#include <stdlib.h>

int main() {
  system("ls -l");
  printf("main end!\n");
}
```
* 結果
    * 執行ls -l指令
```
ubuntu@foo1:~/sp/08-posix/03-fork/04-system$ ./system1
total 32
-rw-rw-r-- 1 ubuntu ubuntu   260 Jun 10 11:48 mysystem0.c
-rw-rw-r-- 1 ubuntu ubuntu   332 Jun 10 11:48 mysystem1.c
-rwxrwxr-x 1 ubuntu ubuntu 16744 Jun 16 21:35 system1
-rw-rw-r-- 1 ubuntu ubuntu    99 Mar 29 10:19 system1.c
main end!
```

#### mysystem1.c ----> 模擬system.c函式
* 步驟
1. gcc mysystem1.c -o mysystem1
2. ./mysystem1
```
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h> 
#include <sys/wait.h>

int mysystem(char *arg[]) {
  if (fork()==0) {                                      //父行程繼續執行main
    execvp(arg[0], arg); // child : exec("ls -l")       //子行程被替換
  }
  int status;                                           
  wait(&status);                                        //等待子行程跑完
  return status;
}

int main() {
  char *arg[] = {"ls", "-l", NULL };
  mysystem(arg);
  printf("main end!\n");
}

```
* 結果與上述相同

#### zombie.c
* 步驟
1. gcc zombie.c -o zombie
2. ./zombie
```
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
int main () {
  pid_t child_pid;
  /* Create a child process. */
  child_pid = fork ();
  if (child_pid > 0) {
    /* This is the parent process. Sleep for a minute. */
    sleep (60);
  } else {
    /* This is the child process. Exit immediately. */
    exit (0);
  }
  return 0;
}
```
* 結果 
    * 用./zombie& 背景觀察
    * 展示讓父行程活的比子行程久---->殭屍行程--->60s結束行程
```
ubuntu@foo1:~/sp/08-posix/03-fork/05-zombie$ ./zombie&
[1] 12455
ubuntu@foo1:~/sp/08-posix/03-fork/05-zombie$ ps
    PID TTY          TIME CMD
  10092 pts/0    00:00:00 bash
  12455 pts/0    00:00:00 zombie
  12456 pts/0    00:00:00 zombie <defunct>
  12457 pts/0    00:00:00 ps
```

#### echo.c---->讀寫檔
* 步驟
1. gcc echo.c -o echo
2. ./echo
```
#include <stdio.h>
#include <unistd.h>
#define SMAX 128

int main() {
  char line[SMAX];
  int n = read(0, line, SMAX); // 從 0 (標準輸入 stdin:鍵盤) 讀入一行字 line
  line[n] = '\0';              // 設定字串結尾
  write(1, line, n);           // 將 line 輸出到 1 (標準輸出 stdout)
  write(2, line, n);           // 將 line 輸出到 2 (標準錯誤 stderr)
}

```
* 結果
    * 將要讀的檔打入
```
ubuntu@foo1:~/sp/08-posix/04-fs/01-echo$ ./echo
brian Hi // 讀檔點
brian Hi
brian Hi
```