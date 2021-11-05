## 法1：使用string的格式化方法
如果需要结果为字符串，可以使用string.format()函数
如，保留两位小数：string.format("%.2f",num)

- 5舍6入
需要注意的是该方法会对末尾5舍6入，比如
```lua
print("x5:"..string.format("%.2f",1.115))
print("x6:"..string.format("%.2f",1.116))
```
结果为x5:1.11; x6:1.12
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