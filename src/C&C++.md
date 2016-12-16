#预处理器
##宏定义
do{...}while(0) 宏定义
使用do{...}while(0)构造后的宏定义完全等价于一条语句，不会受到大括号、分号等的影响，总是会按你期望的方式调用运行。另外，通过使用break和do...while(0)还能消除goto语句。使用空的do{}while(0)能定义一个空宏，避免编译警告。
比如：
```
#define foo(x) bar(x); baz(x)
if(true)
  foo(x); # 逻辑错误，实际只有第一个语句在if语句中

#define foo(x)  { bar(x); baz(x); }
if(true)
  foo(x); # 语法错误，大括号后有分号';' 
else
  bin(x);

#define foo(x)  do{ bar(x); baz(x); }while(0)
if(true)
  foo(x); # 正确
else
  bin(x);
```
