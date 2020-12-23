Lua中for循环的格式为：
```lua
for var=exp1,exp2,exp3 do  
    <执行体>  
end  
```

var 从 exp1 变化到 exp2，每次变化以 exp3 为步长递增 var，并执行一次 "执行体"。exp3 是可选的，如果不指定，默认为1。

for的三个表达式在循环开始前一次性求值，以后不再进行求值。
无论表达式如何，执行体都会执行一次。诸如以下代码：
```
for var=1,0 do  
    print(var)
end 
```
print都会执行一次