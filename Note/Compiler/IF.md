# 程式如何編譯if

* code
檔案<https://github.com/brian891005/sp109b/blob/main/Note/Compiler/compiler.c>
```
//IF = if (E) STMT
void IF() {
  int ifMid = nextLabel(); //在一個迴圈做一次標記;
  int ifEnd = nextLabel(); //迴圈結尾else做標記;
  skip("if");
  skip("(");
  int e = E();
  emit("if not T%d goto L%d\n", e, ifMid);
  skip(")");
  STMT();
  emit("goto L%d\n",ifEnd);
  emit("(L%d)\n", ifMid);
  if (isNext("else")) {          //當程式有else
    skip("else");
    STMT();
    emit("(L%d)\n",ifEnd);       //標示else||else if結束
  }
  else{
    emit("(L%d)\n",ifEnd);      //當沒有else時
  }
}
```

* if1.c  code
檔案<https://github.com/brian891005/sp109b/blob/main/Note/Compiler/test/if1.c>
```
i=3;
if(i<3){
    i=5;
}
```

* if1.c執行結果 沒有else照樣能編譯

```
t0 = 3
i = t0
t1 = i
t2 = 3
t3 = t1 < t2
if not T3 goto L0
t4 = 5
i = t4
goto L1
(L0)
(L1)
```

* if2.c code
檔案<https://github.com/brian891005/sp109b/blob/main/Note/Compiler/test/if2.c>
```
a=5;
if (a>3) {
  t=1;
} else if(a<9){
  t=2;
} else{
  t=3;
}
```

* if2.c執行結果  能通用else if迴圈
```
t0 = 5
a = t0
t1 = a
t2 = 3
t3 = t1 > t2
if not T3 goto L0
t4 = 1
t = t4
goto L1
(L0)
t5 = a
t6 = 9
t7 = t5 < t6
if not T7 goto L2
t8 = 2
t = t8
goto L3
(L2)
t9 = 3
t = t9
(L3)
(L1)
```
