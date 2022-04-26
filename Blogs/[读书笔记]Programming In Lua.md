## 第1篇 语言
### 第0章 序言
Programming In Lua针对的Lua版本为5.0。笔者在Lua5.3环境中学习，部分笔记会根据版本进行修正。

### 第1章 起点
#### 1.1 Chunks
Chunk是一系列语句，Lua执行的每一块语句，比如一个文件或者交互模式下的每一行都是一个Chunk。一个Chunk可以是一个语句，也可以是一系列语句的组合，还可以是函数。

#### 1.2 全局变量
给一个变量赋值后即创建了这个全局变量，如``b=10``。如若想删除一个全局变量只需要将变量赋值为nil

#### 1.3 注释
单行：--内容
多行:--[[ 内容 ]]

### 第2章 类型和值
Lua是动态类型语言，变量无需定义类型。Lua中8个基本类型为：nil,boolean,number,string,userdata,function,thread,table。函数type()可以获得给定变量或者值的类型,type函数的返回类型为string。
```lua
print(type("Hello world"))  --> string
print(type(print))          --> function
print(type(type))           --> function
print(type(true))           --> boolean
print(type(3*6))            --> number
print(type(nil))            --> nil
print(type(a))              --> nil
```

```lua
local b=type(a)
local c=type(b)
print("b:"..b)         --> b:nil
print("c:"..c)         --> c:string
```
a未声明为nil，则b="nil"，因此c="string"

#### 2.1 boolean
Lua的控制结构中除了false和nil为假外，其它都为真。比如0和空字符串都是真。
#### 2.2 string
Lua中一般使用双引号或者单引号来表示字符串，如``local a="hello world"``。若字符串中包含引号需要使用转义字符``\``。
还可以使用[[...]]表示字符串，这种方式字符串可以包含多行，~~可以嵌套~~（Lua5.3测试不可嵌套）且不会解释转义序列，如果第一个字符是换行符会被自动忽略掉。如：
```html
local page = [[
<HTML>
<HEAD>
<TITLE>An HTML Page</TITLE>
</HEAD>
<BODY>
Lua
\\jack\"hello\"
</BODY>
</HTML>
]]

print(page)
```
输出结果为：
```html
<HTML>
<HEAD>
<TITLE>An HTML Page</TITLE>
</HEAD>
<BODY>
Lua
\\jack\"hello\"
</BODY>
</HTML>
```

字符串转数字可以使用 ``tonumber()`` 方法