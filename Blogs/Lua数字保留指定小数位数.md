## 法1：使用string的格式化方法
如果需要结果为字符串，可以使用string.format()函数
如，保留两位小数：string.format("%.2f",num)

- 5舍6入，还是4舍5入
比如
```lua
print("x0:"..string.format("%.0f",1.4))
print("x1:"..string.format("%.0f",1.5))
print("x2:"..string.format("%.0f",1.6))
print("x3:"..string.format("%.1f",1.159))
print("x4:"..string.format("%.1f",1.169))
print("x5:"..string.format("%.2f",1.1159))
print("x6:"..string.format("%.2f",1.1169))
```
笔者在unity中使用xlua测试，遇到过两种结果。
结果一为 x0:1; x1:2; x2:2; x3:1.2; x4:1.2; x5:1.12; x6:1.12
结果二为 x0:1; x1:2; x2:2; x3:1.1; x4:1.2; x5:1.11; x6:1.12

- 不足补0
对于位数不足的数，结果会补0，比如
```lua
print("x7:"..string.format("%.2f",1.1))
print("x8:"..string.format("%.2f",1))
```
结果为：x7:1.10; x8:1.00

## 法2：使用数学计算方法

易知num%1可以得到num的小数部分，num-num%1可以得到其整数部分。

故num-num%(1/(10^n))可以得到num的小数点后n位（n>=0）