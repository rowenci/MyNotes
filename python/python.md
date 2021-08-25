# python

https://www.liaoxuefeng.com/wiki/1016959663602400

## 1. 集合

这是python当中两种集合类型。作为一种动态语言，python不需要提前声明变量类型，你直接写就行了

### list

list是一种有序的集合，可以随时添加和删除其中的元素。

```python
nums = [1, 2, 3, 'rowenci']
print(nums)

###
[1, 2, 3, 'rowenci']
###
```

nums[-1]获取最后一个元素。还可以-2，-3...依次从后往前类推



list方法

> append(obj)	添加元素
>
> insert(index, obj)	将元素插入到指定位置
>
> pop()	删除末尾元素
>
> pop(index)	删除指定位置元素



### tuple

另一种有序列表叫元组：tuple。tuple和list非常类似

但是**tuple一旦初始化就不能修改**

```python
nums = (1, 2, 3, 'rowenci')
```

注意与list在写法上的区别，一个是[]另一个是()

要定义一个只有1个元素的tuple，如果你这么定义：

```python
num = (1)
```

定义的不是tuple，是`1`这个数！这是因为括号`()`既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是`1`。

所以，只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义：

```
>>> t = (1,)
>>> t
(1,)
```

Python在显示只有1个元素的tuple时，也会加一个逗号`,`，以免你误解成数学计算意义上的括号。



### dict

dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。不可以放入可变对象

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95
```

把数据放入dict的方法，除了初始化时指定外，还可以通过key放入：

```python
>>> d['Adam'] = 67
>>> d['Adam']
67
```

要避免key不存在的错误，有两种办法，一是通过`in`判断key是否存在：

```python
>>> 'Thomas' in d
False
```

> get(key)	返回对应的value
>
> get(key, obj)	如果key不存在，返回obj
>
> pop(key)	删除对应key



### set

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。同样不可以放入可变对象

重复元素在set中自动被过滤：

```python
>>> s = set{1, 1, 2, 3}
>>> s
{1, 2, 3}
```

> add(key)	添加元素
>
> remove(key)	删除元素

set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：

```python
>>> s1 = set([1, 2, 3])
>>> s2 = set([2, 3, 4])
>>> s1 & s2
{2, 3}
>>> s1 | s2
{1, 2, 3, 4}
```



## 2. 条件判断

```python
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```



## 3. 循环

第一种，for循环

```python
sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    sum = sum + x
print(sum)
```

第二种，while循环

```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

与java相同，也可以break和continue



## 4. 常用内置函数

### 数据类型转换

```python
>>> int('123')
123
>>> int(12.34)
12
>>> float('12.34')
12.34
>>> str(1.23)
'1.23'
>>> str(100)
'100'
>>> bool(1)
True
>>> bool('')
False

可以用help(函数名)来查看对应内置函数的作用
例如
'''
help(abs)
Help on built-in function abs in module builtins:

abs(x, /)
    Return the absolute value of the argument.
'''
```

