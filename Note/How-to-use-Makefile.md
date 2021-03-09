## MakeFile:
當程式越來越多時，編譯、連結與測試的動作會變得相當繁瑣，此時就必須使用專案建置工具。透過 make 觀察大型專案的開發過程，讓讀者得以學習到專業的系統程式開發流程。make 專案管理工具相當方便。
## Makefile 特殊符號

```
$@ : 該規則的目標文件 (Target file)
$* : 代表 targets 所指定的檔案，但不包含副檔名
$< : 依賴文件列表中的第一個依賴文件 (Dependencies file)
$^ : 依賴文件列表中的所有依賴文件
$? : 依賴文件列表中新於目標文件的文件列表

?= 語法 : 若變數未定義，則替它指定新的值。
:= 語法 : make 會將整個 Makefile 展開後，再決定變數的值。
```

## 使用方式:
簡單範例(使用make，簡化指令，製造執行檔)
<pre><code>
CC := gcc
CFLAGS = -std=c99 -O0
TARGET = run

all: $(TARGET)

$(TARGET): sum.c main.c
	$(CC) $(CFLAGS) $^ -o $@

clean:
	rm -f *.o *.exe $(TARGET)
</code></pre>
