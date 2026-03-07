---
title: "位移（Transform）"
author: "mona"
date: 2024-06-17T11:42:00+08:00
categories: ["Unity"]
tags: ["向量", "MonoBehavior", "位移"]
---

### 计算位移量

路程=方向\*时间\*速度

**方式一：自己计算**

自己当前的位置+向量（方向）\*速度\*每一帧需要的时间 = 1秒移动1

```c#
transform.position = transform.position + transform.forward(本地坐标向前 对应世界坐标的向量) * 1 * Time.deltaTime;
//transform.forward(本地坐标向前 对应世界坐标的向量)
```

**方法二：API移动**

```c#
//参数一：位移多少 方向*速度*时间
//参数二：表示相对坐标系 默认参数是 物体自己的坐标系Space.Self 可选世界坐标Space.World
transform.Translate(Vector3.forward*1*Time.deltaTime);
//也可以使用世界坐标系 使用transform.forward作为向量
transform.Translate(transform.forward*1*Time.deltaTime,Space.World);
```

### 角度和旋转

- 和position一样也有相对世界坐标的角度和相对父对象的角度
- transform.rotation的值是一个四元数 面板上显示的是欧拉角可通过transform.eulerAngles参数获取或者修改
- 和设置位置一样不可以单独设置xyz ~~transform.eulerAngles.x = 50~~
- 通过localEulerAngles获取的角度不会是负数虽然面板显示负数，只能获得0~360之间的数

**修改角度（欧拉角）**

```c#
//世界坐标角度
transform.eulerAngles = new Vector3(0,1,0);
//相对父对象角度
transform.localEulerAngles = new Vector3(0,1,0);
```

**通过API旋转**

```c#
//默认使用自己的坐标系 进行旋转
transform.Rotate(Vector3.forward * 30 * Time.deltaTime)
//使用世界坐标系进行旋转
tank.transform.Rotate(Vector3.forward * 30 * Time.deltaTime,Space.World);
```

**相对于某个轴旋转**

```c#
//默认使用自己的坐标系 Z轴旋转
transform.Rotate(Vector3.forward,30*Time.deltaTime);
//默认使用世界坐标系 Z轴旋转
transform.Rotate(Vector3.forward,30*Time.deltaTime,Space.World);
```

**相对于某一个点旋转**

```c#
//第一个参数 围绕旋转的点（是一个世界坐标）
//第二个参数 旋转的轴
//旋转的速度
transform.RotateAround(Vector3.zero,Vector3.up, 10 * Time.deltaTime);
```

### 缩放

- 缩放也分成相对世界坐标系的缩放 和相对本地坐标系的缩放（父对象）
- 也不可以直接修改xyz ~~transform.localScale.x=2~~
- 相对世界坐标系的缩放只可以获得 不可以修改

```c#
//相对世界坐标系 不可修改
Vector3 v1 = transform.lossyScale;
//相对本地坐标系
transform.localScale = new Vector3(3,3,3);
```

### 看向

让一个对象的面朝向 可以一直看向某一个点或者某一个对象

```c#
//看向一个点 相对于世界坐标系
transform.LookAt(Vector3.zero);
//看向一个对象
transform.LookAt(obj.transform);
```

### 父子对象

**获取和设置父对象**

```c#
//获取父对象
transform.parent;
//设置父对象为空
transform.parent = null;
//通过api设置
transform.SetParent(null);
//是否保留世界坐标的位置角度缩放信息 默认true（会重新计算 本地坐标下的位置信息 设置父对象后物体位置不会改变）
//不保留则会把世界坐标的属性加到本地坐标上
transform.SetParent(obj,false);
```

 **和所有的子对象断绝关系**

```c#
transform.DetachChildren();
```

**获取子对象**

- transform的Find方法是可以找到失活的对象的 GameObject的Find不能找到失活对象
- 只能找到自己的子对象 不能找到子对象的子对象
- 相对GameObject查找来说 效率会高一些

```c#
transform.Find("childname");
```

**遍历子对象**

- 失活的子对象也算数量
- 无法找到子对象的子对象

```c#
//获取子对象的数量
int count = transform.childCount;
//通过索引号 去得到自己对应的子对象
for (int i = 0; i < count; i++)
{
    //通过索引号 来获取子对象 返回值是transform
    Debug.Log(transform.GetChild(i).name);
}
```

**子对象的操作**

**判断一个对象是不是另一个对象的子节点**

```c#
if(son.IsChildOf(fater)){
	print("是我儿子");
}
```

**得到自己的子节点编号**

```c#
transform.GetSiblingIndex();
```

**设置子节点的编号**

```c#
//设置为第一个子节点
transform.SetAsFirstSibiling();
//设置为最后一个子节点
transform.SetAsLastSibiling();
//设置自己的子节点编号
transform.SetSibilingIndex();
```

### 父子关系练习题

写一个扩展Transform的方法 要求对子对象的名字进行排序

```C#
//扩展方法需要写成静态类
public static class Tools{
    //静态方法
    //参数填写需要扩展的类
	public static void Sort(this Transform obj){
        List<Transform> childList = new List<Transform>();
        for( int i = 0 ; i < obj.childCount ; i++ ){
            childList.Add(obj.GetChild(i));
        }
        childList.Sort((a,b)=>{
            if(a.name.Length>b.name.Length){
                return 1;
            }else{
                return -1
            }
        });
        for(int i = 0; i < childList.count; i++){
           	childList(i).SetSibilingIndex(i);
        }
    }
}
```

写一个扩展Transform的方法 要求传入名字查询子对象 子对象的子对象也要找到

```c#
//扩展方法需要写成静态类
public static class Tools{
	public static Transform FindChild(this Transform feter , string name){
        Transform target = feter.Find(name);
        if(tagert!=null) return target;
        for(int i = 0; i < feter.childCound; i++){
            target = feter.GetChild(i).FindChild(name);
            if(target != null) return target;
        }
        return target;
    }
}
```