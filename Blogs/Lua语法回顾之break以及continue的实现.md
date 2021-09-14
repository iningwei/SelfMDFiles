## break
break是lua语言的关键字，用于循环体中（如while，for等循环体）跳出该循环体，并继续执行紧接着的语句。若是循环嵌套，则break语句会停止最内层循环的执行，并开始执行外层循环语句。这一点和我们平常使用的其它语言是一致的。示例如下：
```c
    for i = 1, 2 do
        for j = 1, 5 do
            print("i:"..i..", j:"..j)
            if j==3 then
                break;
            end
        end
    end
```
上例中，当j==3时，内部for循环跳出，继续执行外部for循环

```c
    local a=0
    while a<5 do
        do
            print("a:"..a)
            a=a+1
            if a>3 then
                break
            end
        end
    end
```
上例中，当a的值等于4时，会跳出while循环

## continue
lua中并没有该关键字，可以借助while循环来实现该需求。如下例：
```c
    for i = 1, 5 do
        while true do
            if i==3 then
                break
            else                
                print("i:"..i)
                --TODO things
                break
            end
        end
    end
```
上例当i==3时会跳过输出，只会输出1，2，4，5

特别注意while循环里面else内部也要使用break跳出循环，否则会导致死循环