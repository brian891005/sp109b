# 虛擬機與連結器

## 虛擬機
* 狹義的虛擬機
  * 虛擬機:模擬處理器指令集的軟體
  * 模擬器:模擬電腦行為的軟體
* 廣義的虛擬機
  * 虛擬機+模擬器

## 虛擬機的種類

![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/Vm.jpg)

* 寄生式虛擬機:虛擬機跑在作業系統下
* 程序虛擬機: 有自己的指令集，ex:JVM

## 中間碼
這個術語源自於編譯器，在編譯器將原始碼編譯為目的碼的過程中，會先將原始碼轉換為一個或多個的中間表述，以方便編譯器進行最佳化，並產生出目的機器的機器語言

### 虛擬機三架構
* 記憶體機
    * 一般的虛擬機
* 暫存器機
    * 有固定暫存器可以把變數載入
* 堆疊機
    * 把堆疊的變數抓下來做運算
    * 可以變相成長，但有極限
    
![Pic](https://github.com/brian891005/sp109b/blob/main/Note/IMG/StackMachine.jpg)

## 虛擬機java使用
* 先以sudo apt install default-jre 下載java編譯器
* 以HelloWorld.java當例子
    * javac HelloWorld.java ------>>>編譯檔案跑出class檔
    * javap -c HelloWorld.class ------->>>將java程式還原成組合語言

* 組合語言檔
```
Compiled from "HelloWorld.java"
class HelloWorld {
  HelloWorld();   
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #13                 // String Hello World!
       5: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

## 虛擬機QEMU
* 作業系統開法
* 一個平台跑多個平台程式

## C4
* C4 編譯完成後，會產生一種《堆疊機機器碼》放在記憶體內，然後 [虛擬機](vm) 會立刻執行該機器碼。
* 可以編譯自己的編譯器
* C4 在 Windows / Linux / MAC 中都可以執行，在 windows 必須在有支援 open, read 這些 POSIX 檔案函式庫環境下才能編譯！
#### 支援的語法

項目 | 語法
-----|-------------------
判斷 | if ... else
迴圈 | while (...)
區塊 | {...}
函數呼叫 | f()
函數定義 | int f(....)
傳回值 | return 
陣列存取 | a[i] 
數學運算 | +-*/%, ++, --, +=, -=, *=, /=, %=
位元運算 | &|^~
邏輯運算 |  ! && || 
列舉 | enum ...
運算式 | (a*3+5)/b 
指定 | x = (a*3+5)/b
取得大小 | sizeof
強制轉型 | (int*) ptr; (char) a;
基本型態 | int, char
指標 | *ptr 
遞迴 | int f(n) { ... return f(n-1) + f(n-2); }
陣列存取 | a[i]
