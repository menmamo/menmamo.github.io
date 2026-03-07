---
title: "xlua"
author: "mona"
date: 2024-09-24T14:51:00+08:00
categories: ["Unity"]
tags: ["热更新"]
---

## Lua解析器

Lua解析器能够让我们在Unity中执行Lua

默认的Lua脚本放在`Resources`文件夹下，并且要加上`.txt`后缀 才能被执行

```c#
LuaEnv env = new LuaEnv();
env.DoString("print('你好世界')");
//执行一个lua脚本
env.DoString("require('Main')");
```

**垃圾回收**

帮助我们清楚Lua中我们没有手动释放的对象 垃圾回收

帧更新中定时执行 或者 切场景时执行

```c#
env.Tick();
```

**销毁Lua解析器**

```c#
env.Dispose();
```

------

## Lua文件加载重定向

xlua提供的一个 路径重定向 的方法

允许我们自定义 加载 Lua文件的规则

当我们执行Lua语言 `require`时 相当于执行一个lua脚本

它就会 执行 我们自定义传入的这个函数

```c#
LuaEnv env = new LuaEnv();
env.AddLoader(MyCustomLoader);
env.DoString("require('Main')");
private byte[] MyCustomLoader(ref string filePath)
{
    //通过函数中的逻辑 去加载 Lua文件 
    //传入的参数 是 require执行的lua脚本文件名
    //拼接一个Lua文件所在路径
    string path = Application.dataPath + "/Lua/" + filePath + ".lua";
    if ( File.Exists(path) )
    {
        return File.ReadAllBytes(path);
    }
    return null;
}
```

------

## C#调用Lua

### 获取全局变量

**获取大G表**

```c#
LuaEnv env = new LuaEnv();
LuaTable global = env.Global;
```

**获取值**

```c#
global.Get<int>("number");
global.Get<bool>("sex");
global.Get<float>("testFloat");
global.Get<double>("testDouble");
global.Get<string>("testString");
```

**设置值**

```c#
global.Set("number",12);
```

------

### 获取全局函数

**无参无返回的获取**

```c#
//通过自定义委托获取
public delegate void CustomCall();
CustomCall call = global.Get<CustomCall>("testFun");
//Unity自带委托
UnityAction ua = global.Get<UnityAction>("testFun");
//C#提供的委托
Action ac = global.Get<Action>("testFun");
//Xlua提供的一种 获取函数的方式 少用
LuaFunction lf = global.Get<LuaFunction>("testFun");
```

**有参有返回获取**

```c#
//通过自定义委托获取
//有参有返回 的委托
//该特性是在XLua命名空间中的
//加了过后 要在编辑器里 生成 Lua代码
[CSharpCallLua]
public delegate int CustomCall2(int a);
CustomCall2 call2 = global.Get<CustomCall2>("testFun2");
//C#自带的泛型委托 方便我们使用
Func<int, int> sFun = global.Get<Func<int, int>>("testFun2");
//Xlua提供的
LuaFunction lf2 = global.Get<LuaFunction>("testFun2");
```

**多返回值获取**

```c#
//自定义委托 参数中添加out关键字的方式接收
[CSharpCallLua]
public delegate int CustomCall3(int a, out int b, out bool c, out string d, out int e);
CustomCall3 call3 = global.Get<CustomCall3>("testFun3");
int b;
bool c;
string d;
int e;
Debug.Log("第一个返回值：" + call3(100, out b, out c, out d, out e));
Debug.Log(b + "_" + c + "_" + d + "_" + e);
//自定义委托 参数中添加ref关键字的方式接收
[CSharpCallLua]
public delegate int CustomCall4(int a, ref int b, ref bool c, ref string d, ref int e);
int b1 = 0;
bool c1 = true;
string d1 = "";
int e1 = 0;
Debug.Log("第一个返回值：" + call4(200, ref b1, ref c1, ref d1, ref e1));
Debug.Log(b1 + "_" + c1 + "_" + d1 + "_" + e1);
//Xlua提供的
LuaFunction lf3 = global.Get<LuaFunction>("testFun3");
//返回值是一个Object数组
object[] objs = lf3.Call(1000);
for( int i = 0; i < objs.Length; ++i )
{
    Debug.Log("第" + i + "个返回值是：" + objs[i]);
}
```

**变长参数**

```c#
//自定义委托
[CSharpCallLua]
public delegate void CustomCall5(string a, params int[] args);//变长参数的类型 是根据实际情况来定的
CustomCall5 call5 = global.Get<CustomCall5>("testFun4");
call5("123", 1, 2, 3, 4, 5, 566, 7, 7, 8, 9, 99);
//xlua自带
LuaFunction lf4 = global.Get<LuaFunction>("testFun4");
lf4.Call("456", 6, 7, 8, 99, 1);
```

------

### List和Dictionary

**获取List**

```c#
//指定类型
List<int> list = global.Get<List<int>>("testList");
//不指定类型
List<object> list3 = global.Get<List<object>>("testList2");
```

**获取 Dictionary**

```c#
//指定类型
Dictionary<string, int> dic2 = global.Get<Dictionary<string, int>>("testDic");
//不指定类型
Dictionary<object, object> dic3 = global.Get<Dictionary<object, object>>("testDic2");
```

------

### 类映射Table

- 类中去声明成员变量名字一定要和 Lua那边的一样
- 必须是公共的 私有和保护 没办法赋值
- 这个自定义中的 变量 可以更多也可以更少
  - 如果变量比 lua中的少 就会忽略它
  - 如果变量比 lua中的多 不会赋值 也会忽略

```c#
public class CallLuaClass
{
    public int testInt;
    public bool testBool;
    public UnityAction testFun;
}
CallLuaClass obj = global.Get<CallLuaClass>("testClass");
```

------

### 接口映射Table

- 接口中是不允许有成员变量的 
- 我们用属性来接受
- 接口和类规则一样 其中的属性多了少了 不影响结果 无非就是忽略他们

```c#
[CSharpCallLua]
public interface ICallLuaClass
{
    int testInt
    {
        get;
        set;
    }
    bool testBool
    {
        get;
        set;
    }
    UnityAction testFun
    {
        get;
        set;
    }
}
ICallLuaClass obj = global.Get<ICallLuaClass>("testClass");
```

**注意：接口拷贝 是引用拷贝 改了值 lua表中的值也变了**

------

### LuaTable映射Table

不建议使用LuaTable和LuaFunction 效率低

```c#
LuaTable table = global.Get<LuaTable>("testClass");
table.Get<int>("testInt")
tables.Set("TestInt",20)
```

------

## Lua调用C#

### 使用C#的类

lua中使用C#的类非常简单

固定套路

`CS.命名空间.类名`

Unity的类 比如 `GameObject` `Transform`等等 —— `CS.UnityEngine.类名`

`CS.UnityEngine.GameObject`

-通过C#中的类 实例化一个对象 lua中没有new 所以我们直接 类名括号就是实例化对象

默认调用的 相当于就是无参构造

```lua
local obj1 = CS.UnityEngine.GameObject()
```

为了方便使用 并且节约性能 可以定义全局变量存储 C#中的类

相当于取了一个别名

```lua
GameObject = CS.UnityEngine.GameObject
local obj3 = GameObject()
```

类中的静态对象 可以直接使用.来调用

```lua
local obj4 = GameObject.Find("GameObject")
```

得到对象中的成员变量  直接对象 . 即可

```lua
obj4.transform.position
Debug = CS.UnityEngine.Debug
Debug.Log(obj4.transform.position)
```

自定义类 使用方法 相同  只是命名空间不同而已

```lua
local t = CS.Test()
-- 成员方法 使用：调用
t:Speak("test说话")
```

继承了Mono的类 是不能直接new的

xlua提供了一个重要方法 typeof 可以得到类的Type

xlua中不支持 无参泛型函数  所以 我们要使用另一个重载

```lua
local obj5 = GameObject("加脚本测试")
obj5:AddComponent(typeof(CS.LuaCallCSharp))
```

------

### 使用C#枚举

枚举的调用规则 和 类的调用规则是一样的

`CS.命名空间.枚举名.枚举成员`

也支持取别名

```lua
PrimitiveType = CS.UnityEngine.PrimitiveType
GameObject = CS.UnityEngine.GameObject
-- 创建一个 立方体
local obj = GameObject.CreatePrimitive(PrimitiveType.Cube)
```

**枚举转换**

```lua
--数值转枚举
local a = E_MyEnum.__CastFrom(1)
--字符串转枚举
local b = E_MyEnum.__CastFrom("Atk")
```

------

### 扩展方法

Lua可以使用C#的扩展方法

但是需要在扩展方法对应的静态类加[LuaCallCsharp]特性 然后生成代码

注意：该特性可以提升Lua访问C\#类的性能，所以建议只要Lua中使用的C\#类都加上该特性

C#类实现：

```c#
[LuaCallCsharp]
public static class TestExpand
{
    public static void TestFun(this Test test)
    {
        Debug.log(test.name);
    }
} 
public class Test
{
    public string name; 
}
```

lua实现：

```lua
local Test = CS.Test()
test:TestFun() -- 使用扩展方法 和使用成员方法一致
```

------

### 使用ref和out函数

**ref**

ref参数 会以多返回值的形式返回给lua

如果函数存在返回值 那么第一个值 就是该返回值

之后的返回值 就是ref的结果 从左到右一一对应

```lua
--ref参数 需要传入一个默认值 占位置
local a,b,c = obj:RefFun(1, 0, 0, 1)
print(a) --函数返回值
print(b) --第一个ref
print(c) --第二个ref
```

```c#
public int ReFun(int a, ref int b, ref int c,int d)
{
    b = a + d;
    c = a - d;
    return 100;
}
```

**out**

out参数 会以多返回值的形式返回给lua

如果函数存在返回值 那么第一个值 就是该返回值

之后的返回值 就是out的结果 从左到右一一对应

out参数 不需要传占位置的值

```lua
local a,b,c = obj:OutFun(20,30)
print(a)--函数返回值
print(b)--第一个out
print(c)--第二个out
```

```c#
public int OutFun(int a, int b, out int c, out int d)
{
	b = a;
	c = d;
	return 200;
}
```

**混合使用**

混合使用时  综合上面的规则

ref需占位 out不用传

第一个是函数的返回值  之后 从左到右依次对应ref或者out

```lua
local a,b,c = obj:RefOutFun(20,1)
print(a)--300
print(b)--200
print(c)--400
```

```c#
public int RefOutFun(int a, int b, out int c, ref int d)
{
    b = a * 10;
    c = a * 20;
    return 300;
}
```

------

### 使用重载函数

虽然Lua自己不支持写重载函数

但是Lua支持调用C#中的重载函数  

```lua
local obj = CS.Test()
print(obj:Calc())
print(obj:Calc(15, 1))
```

Lua虽然支持调用C#重载函数

但是因为Lua中的数值类型 只有Number

对C#中多精度的重载函数支持不好

在使用时 可能出现意想不到的问题

例如：

```c#
public void Calc(int a)
{
    Debug.log(a);
}
public void Calc(float a)
{
    Debug.log(a);
}
```

解决重载函数含糊的问题

xlua提供了解决方案 反射机制

这种方法只做了解 尽量不要使用

Type是反射的关键类

得到指定函数的相关信息

```lua
local m1 = typeof(CS.Test):GetMethod("Calc", {typeof(CS.System.Int32)})
local m2 = typeof(CS.Test):GetMethod("Calc", {typeof(CS.System.Single)})
```

通过xlua提供的一个方法 把它转成lua函数来使用

一般我们转一次 然后重复使用

```lua
local f1 = xlua.tofunction(m1)
local f2 = xlua.tofunction(m2)
--成员方法 第一个参数传对象
--静态方法 不用传对象
print(f1(obj, 10))
print(f2(obj, 10.2))
```

------

### 委托和事件

委托是用来装函数的

使用C#中的委托 就是用来装lua函数的

```c#
//声明委托和事件
public UnityAcion del;
public event UntiyAction eventAction;
public void DoEvent()
{
	eventAction();
}
public void ClearEvent()
{
	eventAction = null;
}
```

**委托添加函数**

Lua中没有复合运算符 不能+=

如果第一次往委托中加函数 因为是nil 不能直接+

所以第一次 要先等=

```lua
local fun = function()
	print("Lua函数Fun")
end
obj.del = fun
-- 添加函数
obj.del = obj.del + fun
-- 添加匿名函数
--不建议这样写 最好最好还是 先声明函数再加
obj.del = obj.del + function( )
	print("临时申明的函数")
end
```

**委托执行**

```lua
obj.del()
```

**委托减函数**

```lua
obj.del = obj.del - fun
```

**清空所有存储的函数**

```lua
obj.del = nil
```

**事件加函数**

事件加减函数  和 委托非常不一样

lua中使用C#事件 加函数 

有点类似使用成员方 冒号事件名("+", 函数变量)

```lua
obj:eventAction("+", fun2)
-- 添加匿名函数 最好不要这样写
obj:eventAction("+", function()
	print("事件加的匿名函数")
end)
```

**事件减函数**

```lua
obj:eventAction("-", fun2)
```

**事件执行**

lua中不可以直接调用事件 需要在C#中实现对应的方法调用

**事件清空**

lua中不可以直接清空事件 可以在C#中实现对应的方法调用

------

### 二维数组

```c#
public int[,] array = new int[2,3]{{1,2,3},{4,5,6}};
```

lua中调用

```lua
local obj = CS.Test()
-- 不可以通过[0][0]来调用
obj.array:GetValue(0,0)
```

------

### 携程

C# 中携程启动都是通过继承了`Mono`的类 通过里面的启动函数 `StartCoroutine`

**开启和关闭携程**

```lua
GameObject = CS.UnityEngine.GameObject
WaitForSeconds = CS.UntiyEngine.WaitForSeconds
-- Xlua提供了一个工具表
-- 里面有很多方法供我们使用
util = require("xlua.util")
-- 在场景中新建一个空物体 然后挂一个脚本上去
local obj = GameObject("携程")
local mono = obj:AddComponent(typeof(CS.Test))
-- 希望被开启的携程函数
fun = function()
	local a = 1
    while true do
        coroutine.yield(WaitForSecond(1))
        print(a)
        a = a+1
        if a > 10 
            --停止携程和C#中一样
            mono:StopCoroutine(b)
    end
end
-- 不可以直接将lua函数传入到携程开启的方法中
-- mono:StartCoroution(fun)
-- 需要使用xlua.util中的cs_generator(lua函数)
b = mono:StartCoroution(util.cs_generator(fun))
```

------

### 调用泛型方法

- lua支持 有约束 有参数 并且参数是泛型的泛型函数
- lua中不支持 没有约束的泛型函数
- lua中不支持有约束 但是没有参数的泛型函数
- lua中不支持 非class的约束

xlua提供了 让上面不支持使用的泛型函数 变得能用

得到通用函数 设置泛型类型 再使用

`xlua.get_generic_method(类，函数名)`

**注意：**

有一定的限制

- Mono打包 这种方式支持
- il2cpp打包 泛型参数是引用类型 才可以使用
- il2cpp打包 如果泛型参数是值类型 除非C#那边已经调用过了 同类型的泛型参数 lua中才可以被使用

C#类：

```c#
public class Test
{
    //lua可以直接调用
    public void TestFun1<T>(T a) where T:GameObject {Debug.Log("有参数有约束")}
    //lua不可以直接调用的
    public void TestFun2<T>(T a){Debug.Log("有参数 没有约束")}
    public void TestFun3<T>() where T:GameObject {Debug.Log("无参数 有约束")}
    public void TestFun4<T>(T a) where T:IList {Debug.Log("有参数和约束 但不是类")}
}
```

lua类：

```lua
local obj = CS.Test()
local testFun2 = xlua.get_generic_method(CS.Test,"TestFun2")
local testFun2_R = testFun(CS.System.Int32) --指定泛型类型
testFun2_R(obj,2) -- 第一个参数传调用函数的对象 第二个参数传入参数
```

------

## 特殊问题

### lua中的nil和C#null 不是相等的

```lua
GameObject = CS.UnityEngine.GameObject
Rigidbody = CS.UnityEngine.Rigibody

local obj = GameObject("Test")
local rig = obj:GatComponent(typeof(Rigidbody))
print(rig) --null

if rig == nil then
    rig = obj:AddComponent(typeof(Rigidbody))
end
print(rig) --null

if rig:Equals(nil) then  -- 调用Object中的方法Equals进行比较 前提是对象是一个Object
    rig = obj:AddComponent(typeof(Rigidbody))
end
```

保险的判断方式

```lua
if rig == nil or rig:Equals(nil) then
end
```

------

### 让系统类型和Lua互相访问

在lua中为`Slider`添加事件 会报错

因为` AddListener`中的参数类型为`UnityAction`

`UnityAction`是系统提供的委托 我们无法直接为系统类添加`[CSharpCallLua]`特性

```lua
GameObject = CS.UnityEngine.GameObject
UI = CS.UnityEngine.UI

local slider = GameObject.Find("Slider")
print(slider)
local sliderScript = slider:GetComponent(typeof(UI.Slider))
print(sliderScript)
sliderScript.onValueChanged:AddListener(function(f)
	print(f)
end)
```

为系统类型添加特性

```c#
public static class Test
{
	[CSharpCallLua]
	public static List<Type> csharpCallLuaList = new List<Type>()
	{
		typeof(UnityAction<float>)
	};
    [LuaCallCSharp]
	public static List<Type> LuaCallCSharpList = new List<Type>()
	{
		typeof(GameObject),typeof(Rigidbody)
	};
}
```

注意：一定要是静态类 和静态List  并且要重新生成代码

------

## 热补丁

1. 在C#脚本中添加特性`[Hotfix]`

2. 添加宏 第一次开发热补丁时需要加
   Project Settings->Player->Scritping Define Symbols->添加`HOTFIX_ENABLE`

3. 生成代码

4. `hotfix` 注入 注入时可能报错 提示要引入tools

   将Tools文件夹 拷贝到 Asset文件夹同级的目录下（工程文件夹根目录）

热补丁的缺点：只要我们修改了热补丁类的代码，我们就需要重新执行第4步

**替换函数**

lua当中 热补丁代码固定写法

```lua
xlua.hotfix(类, "函数名", lua函数)--如果是成员函数 在参数中添加self
```

**替换构造函数**

构造函数和别的函数不同 不是替换原有逻辑 而是先调用原有逻辑 在调用lua逻辑

```lua
xlua.hotfix(CS.HotfixTest,{-- 替换多个函数 通过表
        [".ctor"] = function()
            print("lua热补丁构造函数")
        end,
        -- 析构函数
        Finalize = function()
            print("lua热补丁析构函数")
        end
})
```

**替换携程函数**

```lua
-- 如果要使用携程函数 就要使用Xlua提供的工具表
util = require("xlua.util")
xlua.hotfix(CS.HotfinxTest,{
	TestCoroution = function(self) --携程函数也算是成员属性
        return util.cs_generator(function()
        	while true do
                coroutine.yield(CS.UnityEngine.WaitForSeconds(1)) --等1秒
                print("lua替换c#的携程函数")
            end
        end)
    end
})
```

**替换成员属性**

如果是属性进行热补丁重定向

- set_属性名 设置属性的方法
- get_属性名 得到属性的方法

```lua
xlua.hotfix(CS.HotfinxTest,
{
    set_Age = function(self,v)
        print("lua重定向的属性"..v)
    end,
    get_Age = function(self)
        return 10
    end
})
```

**替换索引器**

- set_Item  索引器设置
- get_Item  索引器获取

```lua
xlua.hotfix(CS.HotfinxTest,
{
    set_Item = function(self,index,v)
        print("设置索引器索引："..index.."值"..v)
    end,
    get_Item = function(self,index,v)
        print("获取索引器索引："..index.."值"..v)
        return 99
    end
})
```

**替换事件操作**

一般不会使用

- add_事件名  代表事件添加操作
- remove_事件名 代表事件减操作

```lua
xlua.hotfix(CS.HotfinxTest,
{
    add_myEvent = function(self,del)
        print(del)
        print("添加事件函数")
        -- 不要把传入的函数往委托事件里存 不然会死循环
        -- self:myEvent("+",del)
        -- 要把传入的函数存入lua中
    end,
    remove_myEvent = function(self,del)
        print(del)
        print("减事件函数")
    end
})
```

**替换泛型类**

lua中替换泛型类 要一个类型一个类型的来

```c#
[Hotfix]
public class HotfixTest2<T>
{
	public void Test(T str)
	{
		Debug.Log(str);
	}
}
```

```lua
xlua.hotfix(CS.HotfixTest(CS.System.String),
    {
        Test = function(self, str)
        	print("lua中打的补丁"..str)
        end
    })
xlua.hotfix(CS.HotfixTest(CS.System.Int32),
    {
        Test = function(self, str)
        	print("lua中打的补丁"..str)
        end
    })
```