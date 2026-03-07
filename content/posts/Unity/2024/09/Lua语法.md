---
title: "Lua语法"
author: "mona"
date: 2024-09-23T15:02:00+08:00
categories: ["Unity"]
tags: ["热更新"]
---

## 变量

**变量声明**

- 不需要声明变量的类型 
- 变量的默认值均为nil
- 变量不声明就可以使用

```lua
--局部变量
local a = 1
-- 全局变量
b = '变量的声明'
```

**变量类型**

简单的四种变量类型：

- number 数字
- string 字符串
- boolean 布尔
- nil 空

复杂的4中变量类型

- function函数
- table 表
- userdata数据结构
- thread协同程序

**得到变量类型**

```lua
type(a) 
```

**lua可以对多个变量赋值**

```lua
a, b = 0, 1 --如果有多余的补空
```

------

## 字符串操作

**获取字符串的长度**

```lua
s = "aBcdEfG字符串"
--一个汉字占3个长度
--英文字符 占1个长度
print(#s)
```

**字符串多行打印**

```lua
--lua中也是支持转义字符的
print("123\n123")
```

**符串拼接**

```lua
--字符串拼接 通过..
print( "123" .. "456" ) --123456
--string.format
print(string.format("我是路飞，我今年%d岁了", 18)) --我是路飞，我今年18岁了
--%d :与数字拼接
--%a：与任何字符拼接
--%s：与字符配对
```

**其他类型转字符串**

```lua
a = true
print(tostring(a))
```

**字符串提供的公共方法**

```lua
str = "abCdefgCd"
--小写转大写的方法
print(string.upper(str))
--大写转小写
print(string.lower(str))
--翻转字符串
print(string.reverse(str))
--字符串索引查找
print(string.find(str, "Cde"))
--截取字符串
print(string.sub(str, 3, 4))
--字符串重复
print(string.rep(str, 2))
--字符串修改
print(string.gsub(str, "Cd", "**"))
--字符转 ASCII码
a = string.byte("Lua", 1)
print(a)
--ASCII码 转字符
print(string.char(a))
```

------

## 运算符

**算数运算符**

- 加法运算符+
- 减法运算符-
- 乘法运算符*
- 除法运算符/
- 整除运算符//(>=lua5.3)
- 取余运算符%
- 幂运算符^

**条件运算符**

- 大于（等于）> >=
- 小于（等于）< <=
- 等于 ==
- 不等于~=

**逻辑运算符**

- and逻辑与
- or逻辑或
- not逻辑非

遵循逻辑运算符“短路”的规则

------

## 条件分支语句

**if语句**

```lua
--if 条件 then.....end
--单分支
a = 9
if a > 5 then
	print("123")
end
-- 双分支
if a < 5 then
	print("123")
else
	print("321")
end
--多分支
if a < 5 then
	print("123")
elseif a == 6 then
	print("6")
elseif a == 7 then
	print("7")
else
	print("other")
end
```

**lua中没有switch语句**

------

## 循环语句

**while循环**

```lua
--while 条件 do ..... end
num = 0
while num < 5 do
	print(num)
	num = num + 1
end
```

**do while循环**

```lua
--repeat ..... until 条件 （注意：条件是结束条件）
num = 0
repeat
	print(num)
	num = num + 1
until num > 5 --满足条件跳出 结束条件
```

**for循环**

```lua
for i =2,5 do --默认递增 i会默认+1
	print(i)
end
for i =1,5,2 do --如果要自定义增量 直接逗号后面写
	print(i)
end
for i =5,1,-1 do --如果要自定义增量 直接逗号后面写
	print(i)
end
```

------

## 函数

**无参函数声明**

```lua
function F1()
	print("F1函数")
end
F2 = function()
	print("F2函数")
end
```

**有参函数声明**

```lua
function F3(a)
	print(a)
end
F4 = function(a)
	print(a)
end
```

**函数的调用**

```lua
-- 无参数的
function F1()
	print("F1函数")
end
F1()
-- 有参数的
function F3(a)
	print(a)
end
--如果你传入的参数 和函数参数个数不匹配
--不会报错 只会补空nil 或者 丢弃
F1(1,2,3)
```

**参数返回值**

```lua
-- lua支持多返回值
--多返回值时 在前面申明多个变量来接取即可
--如果变量不够 不影响 值接取对应位置的返回值
--如果变量多了 不应 直接赋nil
function F4(a)
	return a, "123", true
end
temp, temp2, temp3, temp4 = F4("1")
```

- 函数的类型就是function
- lua不支持重载 默认调用最后一个声明的函数

**变长参数**

```lua
function F7( ... )
	--变长参数使用 用一个表存起来 再用
	arg = {...}
	for i=1,#arg do
		print(arg[i])
	end
end
F7(1,"123",true,4,5,6)
```

**函数嵌套**

```lua
-- 函数可以作为参数返回
function F8()
	return function()
		print(123);
	end
end
f9 = F8()
f9()
F8()()
```

**闭包**

```lua
function F9(x)
	--改变传入参数的生命周期
	return function(y)
		return x + y
	end
end
```

------

## table表

所有的复杂类型都是table（表）

------

### 数组

```lua
a = {1,2,3,true,nil,"test"}
-- lua中的索引从1开始
print(a[1]) --1
--#是通用的获取长度的关键字
--在打印长度的时候 空被忽略
--如果表中（数组中）某一位变成nil 会影响#获取的长度
print(#a)
```

------

**迭代器遍历**

迭代器遍历 主要是用来遍历表的

-#得到长度 其实并不准确 一般不用#来遍历表

**ipairs迭代器遍历**

```lua
--ipairs遍历 还是 从索引1开始往后遍历的 索引小于等于0的值得不到
--还是只能找到连续索引的 键 如果中间断序了 它也无法遍历出后面的内容
a = {[0] = 1, 2, [-1]=3, 4, 5, [5] = 6}
for i,k in ipairs(a) do
	print("ipairs遍历键值"..i.."_"..k)
end
```

**pairs迭代器遍历**

```lua
--它能够把所有的键都找到 通过键可以得到值
for i,v in pairs(a) do
	print("pairs遍历键值"..i.."_"..v)
end
```

------

### 字典

```lua
--字典是由键值对构成 
a = {["name"] = "路飞", ["age"] = 14, ["1"] = 5}
--访问当个变量 用中括号填键 来访问
print(a["name"])
--还可以类似.成员变量的形式得到值
print(a.name)
--虽然可以通过.成员变量的形式得到值 但是不--修改能是数字 例如：a.1
print(a["1"])
--修改值
a["name"] = "LF"
--新增
a.["sex"] = "男"
--删除
a.["sex"] = nil
```

**字典的遍历**

```lua
a = {["name"] = "路飞", ["age"] = 14, ["1"] = 5}
for k,v in pairs(a) do
	--可以传多个参数 一样可以打印出来
	print(k,v)
end
```

------

### 类和结构体

Lua中是默认没有面向对象的 需要我们自己来实现

```lua
Student = 
{
    name = "路飞"
    age = 1,
    srx = true,
    Up = function()
        --如果想要使用表内的成员需要将表作为参数传入 或者指定表名.属性
        Student.age = Student.age + 1
        print("我成长了")
        end,
    Learn = function(t)
		--第二种 能够在函数内部调用自己属性或者方法的 方法
		--把自己作为一个参数传进来 在内部 访问
		print(t.sex)
		print("好好学习，天天向上")
	end
}
```

- C#要是使用类 实例化对象new 静态直接点
- Lua中类的表现 更像是一个类中有很多 静态变量和函数

**调用类中的方法**

```lua
Student.Up()
```

**冒号调用**

```lua
--冒号调用方法 会默认把调用者 作为第一个参数传入方法中
Student:Learn() --等同于Student:Learn(Student)
```

**函数的第三种申明方式**

```lua
function Student:Speak2()
	--lua中 有一个关键字 self 表示 默认传入的第一个参数
	print(self.name .. "说话")
end
Student:Speak2()
```

------

### 表的公共操作

**插入**

```lua
t1 = { {age = 1, name = "123"}, {age = 2, name = "345"} }
t2 = {name = "路飞", sex = true}
table.insert(t1, t2); --把t2插入到t1中
```

**移除**

```lua
--remove方法 传表进去 会移除最后一个索引的内容
table.remove(t1)
--remove方法 传两个参数 第一个参数 是要移除内容的表
--第二个参数 是要移除内容的索引
table.remove(t1, 1)
```

**排序**

```lua
t2 = {5,2,7,9,5}
--传入要排序的表 默认 降序排列
table.sort(t2)
for _,v in pairs(t2) do
	print(v)
end
--传入两个参数 第一个是用于排序的表
--第二个是 排序规则函数
table.sort(t2, function(a,b)
	if a > b then
		return true
	end
end)
```

**拼接**

```lua
tb = {"123", "456", "789", "10101"}
--连接函数 用于拼接表中元素 返回值 是一个字符串
str = table.concat(tb, ",")
```

------

## 多脚本执行

### 全局变量和本地变量

```lua
--全局变量
a = 1
--本地（局部）变量的关键字 local
local b = 2
```

### 多脚本执行

```lua
--关键字 require("脚本名") require('脚本名')
require('Test')
```

### 脚本中可以有返回值

```lua
-- Test脚本
print("Test测试")
testA = "123"
local testLocalA = "456"
print(testLocalA)
return testLocalA
-- Main脚本
print(require('Test')) -- 456
```

### 脚本卸载

```lua
--如果是require加载执行的脚本 加载一次过后不会再被执行
require("Test")
--package.loaded["脚本名"]
--返回值是boolean 意思是 该脚本是否被执行
print(package.loaded["Test"])
--卸载已经执行过的脚本
package.loaded["Test"] = nil
```

### 大G表

```lua
--_G表是一个总表(table) 他将我们申明的所有全局的变量都存储在其中
for k,v in pairs(_G) do
	print(k,v)
end
--本地变量 加了local的变量时不会存到大_G表中
```

------

## 特殊用法

**多变量赋值**

```lua
local a,b,c = 1,2,"123"
--多变量赋值 如果后面的值不够 会自动补空
a,b,c = 1,2 
print(c)--nil
--多变量赋值 如果后面的值多了 会自动省略
a,b,c = 1,2,3,4,5,6
```

**多返回值**

```lua
function Test()
	return 10,20,30,40
end
--多返回值时 你用几个变量接 就有几个值
--如果少了 就少接几个 如果多了 就自动补空
a,b,c = Test()
a,b,c,d,e,f,g,h = Test()
print(h) --nil
```

**逻辑与 逻辑或**

```lua
-- and or 他们不仅可以连接 boolean 任何东西都可以用来连接
-- 在lua中 只有 nil 和 false 才认为是假
-- "短路"——对于and来说  有假则假  对于or来说 有真则真
-- 所以 他们只需要判断 第一个 是否满足 就会停止计算了
print( 1 and 2 )
print( 0 and 1)
print( nil and 1)
print( false and 2)
print( true and 3)
print( true or 1 )
print( false or 1)
print( nil or 2)
```

**lua不支持三目运算符** 

```lua
x = 1
y = 2
-- ? :
local res = (x>y) and x or y
print(res)
```

------

## 协同程序

**协程的创建**

```lua
fun = function()
	print(123)
end
co = coroutine.create(fun)
--协程的本质是一个线程对象
--coroutine.wrap()
co2 = coroutine.wrap(fun)
print(co2)
```

**协程的运行**

```lua
--第一种方式 对应的 是通过 create创建的协程
coroutine.resume(co)
--第二种方式
co2()
```

**协程的挂起**

```lua
fun2 = function( )
	local i = 1
	while true do
		print(i)
		i = i + 1
		--协程的挂起函数
		print(coroutine.status(co3))
		print(coroutine.running())
		coroutine.yield(i)
	end
end
co3 = coroutine.create(fun2)
```

**携程的返回值**

```lua
--默认第一个返回值 是 协程是否启动成功
--yield里面的返回值
isOk, tempI = coroutine.resume(co3)
print(isOk,tempI)
co4 = coroutine.wrap(fun2)
--这种方式的协程调用 也可以有返回值 只是没有默认第一个返回值了
print("返回值"..co4())
```

**协程的状态**

- dead 结束
- suspended 暂停
- running 进行中

```lua
print(coroutine.status(co3))
```

**得到当前正在运行的协程的线程号**

```lua
print(coroutine.running())
```

------

## 元表

- 任何表变量都可以作为另一个表变量的元表
- 任何表变量都可以有自己的元表（爸爸）
- 当我们子表中进行一些特定操作时
- 会执行元表中的内容

**设置元表**

```lua
meta = {}
myTable = {}
--设置元表函数
--第一个参数 子表
--第二个参数 元表（爸爸）
setmetatable(myTable, meta)
```

**特定操作-__tostring**

```lua
meta2 = {
	--当子表要被当做字符串使用时 会默认调用这个元表中的tostring方法
	__tostring = function(t)
		return t.name
	end
}
myTable2 = {
	name = "山治"
}
--设置元表函数
--第一个参数 子表
--第二个参数 元表（爸爸）
setmetatable(myTable2, meta2)
print(myTable2) --山治
```

**特定操作-__call**

```lua
meta3 = {
	--当子表被当做一个函数来使用时 会默认调用这个__call中的内容
	--当希望传参数时 一定要记住 默认第一个参数 是调用者自己
	__call = function(a, b)
		print(a)
		print(b)
	end
}
myTable3 = {}
--设置元表函数
--第一个参数 子表
--第二个参数 元表（爸爸）
setmetatable(myTable3, meta3)
--把子表当做函数使用 就会调用元表的 __call方法
myTable3(1) --1
```

**特定操作-运算符重载**

```lua
meta4 = {
	--相当于运算符重载 当子表使用+运算符时 会调用该方法
	--运算符+
	__add = function(t1, t2)
		return t1.age + t2.age
	end,
	--运算符-
	__sub = function(t1, t2)
		return t1.age - t2.age
	end,
	--运算符*
	__mul = function(t1, t2)
		return 1
	end,
	--运算符/
	__div = function(t1, t2)
		return 2
	end,
	--运算符%
	__mod = function(t1, t2)
		return 3
	end,
	--运算符^
	__pow = function(t1, t2)
		return 4
	end,
	--运算符==
	__eq = function(t1, t2)
		return true
	end,
	--运算符<
	__lt = function(t1, t2)
		return true
	end,
	--运算符<=
	__le = function(t1, t2)
		return false
	end,
	--运算符..
	__concat = function(t1, t2)
		return "567"
	end
}
```

- 如果要用条件运算符 来比较两个对象
- 这两个对象的元表一定要一致 才能准确调用方法

**特定操作-index和newIndex**

- index 当子表中 找不到某一个属性时 
- 会到元表中 index指定的表去找属性
- rawget 当我们使用它是 会去找自己身上有没有这个变量

```lua
meta6Father = {
	age = 1
}
--__index的赋值 写在表外面来初始化
meta6Father.__index = meta6Father
meta6 = {}
setmetatable(meta6, meta6Father)
print(mata6.age) --1
-- 不会去找原表中的index
rawget(mata6, "age")
```

**得到元表的方法**

```
getmetatable(mata6)
```

**newIndex**

```lua
--newIndex 当赋值时，如果赋值一个不存在的索引
--那么会把这个值赋值到newindex所指的表中 不会修改自己
meta7 = {}
meta7.__newindex = {}
myTable7 = {}
setmetatable(myTable7, meta7)
myTable7.age = 1
print(myTable7.age)
--rawset 该方法 会忽略newindex的设置 只会该自己的变量
rawset(myTable7, "age", 2)
print(myTable7.age)
```

------

## 实现OOP

```lua
print("**********面向对象************")
print("**********封装************")
--面向对象 类 其实都是基于 table来实现
--元表相关的知识点
Object = {}
Object.id = 1

function Object:Test()
	print(self.id)
end

--冒号 是会自动将调用这个函数的对象 作为第一个参数传入的写法
function Object:new()
	--self 代表的是 我们默认传入的第一个参数
	--对象就是变量 返回一个新的变量
	--返回出去的内容 本质上就是表对象
	local obj = {}
	--元表知识 __index 当找自己的变量 找不到时 就会去找元表当中__index指向的内容
	self.__index = self
	setmetatable(obj, self)
	return obj
end

--local myObj = Object:new()
--print(myObj)
--print(myObj.id)
--myObj:Test()
--对空表中 申明一个新的属性 教做id
--myObj.id = 2
--myObj:Test()

print("**********继承************")
--C# class 类名 : 继承类
--写一个用于继承的方法
function Object:subClass(className)
	-- _G知识点 是总表 所有声明的全局标量 都以键值对的形式存在其中
	_G[className] = {}
	--写相关继承的规则
	--用到元表
	local obj = _G[className]
	self.__index = self
	--子类 定义个base属性 base属性代表父类
	obj.base = self
	setmetatable(obj, self)
end
--print(_G)
--_G["a"] = 1
--_G.b = "123"
--print(a)
--print(b)

Object:subClass("Person")

local p1 = Person:new()
print(p1.id)
p1.id = 100
print(p1.id)
p1:Test()

Object:subClass("Monster")
local m1 = Monster:new()
print(m1.id)
m1.id = 200
print(m1.id)
m1:Test()

print("**********多态************")
--相同行为 不同表象 就是多态
--相同方法 不同执行逻辑 就是多态
Object:subClass("GameObject")
GameObject.posX = 0;
GameObject.posY = 0;
function GameObject:Move()
	self.posX = self.posX + 1
	self.posY = self.posY + 1
	print(self.posX)
	print(self.posY)
end

GameObject:subClass("Player")
function Player:Move()
	--base 指的是 GameObject 表（类）
	--这种方式调用 相当于是把基类表 作为第一个参数传入了方法中
	--避免把基类表 传入到方法中 这样相当于就是公用一张表的属性了
	--我们如果要执行父类逻辑 我们不要直接使用冒号调用
	--要通过.调用 然后自己传入第一个参数 
	self.base.Move(self)
end

local p1 = Player:new()
p1:Move()
p1:Move()
--目前这种写法 有坑 不同对象使用的成员变量 居然是相同的成员变量
--不是自己的
local p2 = Player:new()
p2:Move()
```

------

## 自带库

### 时间

```lua
--系统时间
print(os.time())
--自己传入参数 得到时间
print(os.time({year = 2014, month = 8, day = 14}))

--os.date("*t")
local nowTime = os.date("*t")
for k,v in pairs(nowTime) do
	print(k,v)
end
print(nowTime.hour)
```

### 数学运算

**绝对值**

```lua
math.abs(-11)
```

**弧度转角度**

```lua
math.deg(math.pi)
```

**三角函数 传弧度**

```lua
math.cos(math.pi)
```

**向下向上取整**

```lua
math.floor(2.6)
math.ceil(5.2)
```

**最大最小值**

```lua
math.max(1,2)
math.min(4,5)
```

**小数分离 分成整数部分和小数部分**

```lua
math.modf(1.2)
```

**随机数**

```lua
--先设置随机数种子
math.randomseed(os.time())
print(math.random(100))
```

**开方**

```
math.sqrt(4)
```

### 路径

```lua
package.path
```

------

## 垃圾回收

```lua
test = {id = 1, name = "123123"}
--垃圾回收关键字
--collectgarbage
--获取当前lua占用内存数 K字节 用返回值*1024 就可以得到具体的内存占用字节数
print(collectgarbage("count"))
--lua中的机制和C#中垃圾回收机制很类似 解除羁绊 就是变垃圾
test = nil
--进行垃圾回收 理解有点像C#的 GC
collectgarbage("collect")

print(collectgarbage("count"))

--lua中 有自动定时进行GC的方法
--Unity中热更新开发 尽量不要去用自动垃圾回收
```