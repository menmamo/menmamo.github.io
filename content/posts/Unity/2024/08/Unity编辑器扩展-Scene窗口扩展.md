---
title: "Unity编辑器扩展-Scene窗口扩展"
author: "mona"
date: 2024-08-08T15:07:00+08:00
categories: ["Unity"]
tags: ["编辑器扩展"]
---

## Scene窗口扩展

### Scene窗口更新相应函数

OnSceneGUI函数会在 选中挂在自定义脚本的对象时自动执行

选中该对象时才会执行

```c#
[CustomEditor(typeof(SceneTest))]
public class SceneTestEditor : Editor
{
    private void OnSceneGUI()
    {
        Debug.Log("Scene窗口扩展相关逻辑")
    }
}
public class SceneTest : MonoBehaviour{}
```

### 自定义窗口中监听Scene窗口更新相应函数

```c#
//SceneView.duringSceneGUI += 事件函数;
public class void TestWindow : EditorWindow
{
    [MenuItem("/Unity编辑器扩展/Test/打卡窗口")]
    private static void OpenWindow()
    {
        GetWindow<TestWindow>().Show();
    }  
    private void OnEnable()
    {
        SceneView.duringSceneGUI += (view)=>
        {
            Debug.Log("自定义窗口扩展Scene");
        };
    }
}
```

## Handles公共类

Handles类提供了很多API

让我们可以在Scene窗口中绘制我们的自定义内容

它和GUI、EditorGUI类似，只不过它专门提供给Scene窗口使用

**Handles中的颜色控制**

```c#
Handles.color = new Color(0, 1, 1, 0.3f);
```

 **Handles中的文本控件**

```c#
Handles.Label(显示位置, 文本内容);
```

**Handles中的线段**

```c#
Handles.DrawLine(起点, 终点, 粗细);
```

**Handles中的虚线**

```c#
Handles.DrawDottedLine(起点, 终点, 粗细);
```

**Handles中的弧线（圆弧）**

```c#
//绘制线框弧线
//Handles.DrawWireArc(圆心, 法线, 绘制朝向, 角度, 半径); 
// 绘制填充弧线
///Handles.DrawSolidArc(圆心, 法线, 绘制朝向, 角度, 半径); 
Handles.color = Color.white;
Handles.DrawWireArc(obj.transform.position, Vector3.up, obj.transform.forward, 30, 5);
Handles.DrawSolidArc(obj.transform.position, obj.transform.up, Quaternion.Euler(0,-15,0)* obj.transform.forward, 30, 4);
```

**Handles中的圆**

```c#
//绘制填充圆
//Handles.DrawSolidDisc(圆心, 法线, 半径); 
//绘制线框圆
//Handles.DrawWireDisc(圆心, 法线, 半径); 
Handles.color = Color.gray;
Handles.DrawSolidDisc(obj.transform.position, obj.transform.up, 2);
Handles.DrawWireDisc(obj.transform.position, obj.transform.up, 3);
```

**Handles中的立方体**

```c#
//Handles.DrawWireCube(中心点, xyz大小);
Handles.DrawWireCube(obj.transform.position, Vector3.one);
```

**Handles中的几何体**

```c#
//Handles.DrawAAConvexPolygon(几何体各顶点);
 Handles.DrawAAConvexPolygon(Vector3.zero, Vector3.right, Vector3.right + Vector3.forward, Vector3.forward);
```

**Handles中的移动轴**

```c#
//Vector3 Handles.DoPositionHandle(位置, 角度);
//Vector3 Handles.PositionHandle(位置, 角度);
obj.transform.position = Handles.DoPositionHandle(obj.transform.position, obj.transform.rotation);
```

**Handles中的旋转轴**

```c#
//Quaternion Handles.DoRotationHandle(角度, 位置);
//Quaternion Handles.RotationHandle(角度, 位置);
 obj.transform.rotation = Handles.RotationHandle(obj.transform.rotation, obj.transform.position);
```

**Handles中的缩放轴**

```c#
//Vector3 Handles.DoScaleHandle(缩放, 位置, 角度, HandleUtility.GetHandleSize(位置));
//Vector3 Handles.ScaleHandle(缩放, 位置, 角度, HandleUtility.GetHandleSize(位置));
 obj.transform.localScale = Handles.DoScaleHandle(obj.transform.localScale, obj.transform.position, obj.transform.rotation, HandleUtility.GetHandleSize(obj.transform.position));
//HandleUtility.GetHandleSize方法的作用是
//获取给定位置的操纵器控制柄的世界空间大小
//使用当前相机计算合适的大小
//它决定了控制柄的缩放大小
```

**Handles中的自由移动**

- 一个不受约束的移动控制柄
- 这个把手可以在所有方向上自由移动

```c#
//Vector3 Handles.FreeMoveHandle(位置, 句柄大小, 移动步进值(按住ctrl键时会按该单位移动), 渲染控制手柄的回调函数);句柄大小一般配合HandleUtility.GetHandleSize函数使用
//渲染控制手柄的常用回调函数:
        //Handles.RectangleHandleCap:一个矩形形状的控制手柄，通常用于表示一个平面的控制面
        //Handles.CircleHandleCap:一个圆形的控制手柄，通常用于表示一个球体的控制面
        //Handles.ArrowHandleCap:一个箭头形状的控制手柄，通常用于表示方向
obj.transform.position = Handles.FreeMoveHandle(obj.transform.position, HandleUtility.GetHandleSize(obj.transform.position),Vector3.one*5, Handles.CircleHandleCap);
```

**Handles中的自由旋转**

```c#
//Quaternion Handles.FreeRotateHandle(角度, 位置, 句柄大小);
obj.transform.rotation = Handles.FreeRotateHandle(obj.transform.rotation, Vector3.zero, HandleUtility.GetHandleSize(Vector3.zero));
```

### Scene窗口中显示GUI

```c#
private void OnSceneGUI()
{
    Handles.BeginGUI();
    //GUI
    Handles.EndGUI();
}
```

**获取Scene窗口大小**

- SceneView.currentDrawingSceneView
- 它继承自EditorWindow，因此通过position就能得到它的大小

```c#
private void OnSceneGUI()
{
    Handles.BeginGUI();
    //GUI
    if(GUILayout.Button("测试按钮"))
    {
        Debug.Log("Scene中的按钮响应");
    }
    //获取Scene窗口宽高
    float w = SceneView.currentDrawingSceneView.position.width;
    float h = SceneView.currentDrawingSceneView.position.height;
    // 创建一个GUILayout块
    GUILayout.BeginArea(new Rect(w - 100, h - 100, 100, 100));
    GUILayout.Label("测试文本控件显示");
    if (GUILayout.Button("测试按钮"))
    {
        Debug.Log("Scene中的按钮响应");
    }
    GUILayout.EndArea();
    Handles.EndGUI();
}
```

## HandleUtility公共类

HandleUtility是 Unity 中的一个工具类

用于处理场景中的编辑器句柄（Handles）以及其他一些与编辑器交互相关的功能

它提供了一系列静态方法，用于处理编辑器中的鼠标交互、坐标转换以及其他与Handles相关的功能

### HandleUtility类中的常用API

 **获取在场景中给定位置的句柄的合适尺寸**

```c#
GetHandleSize(Vector3 position)
```

**将世界坐标转换为 GUI 坐标**

通常用于将场景中的某个点的位置转换为屏幕上的像素坐标

```c#
//WorldToGUIPoint(Vector3 worldPosition)
Vector2 pos = HandleUtility.WorldToGUIPoint(obj.transform.position);
Handles.BeginGUI();
GUI.Button(new Rect(pos.x, pos.y, 50, 20), "按钮会跟着物体移动");
Handles.EndGUI();
```

**将屏幕上的像素坐标转换为射线**

可以理解为是Scene场景的摄像机 发射一条射线

```c#
//GUIPointToWorldRay(Vector2 position)
Ray r = HandleUtility.GUIPointToWorldRay(Event.current.mousePosition);//得到一条射线
RaycastHit info;
if (Physics.Raycast(r, out info))
    Debug.Log(info.collider.name);
```

**计算场景中一条线段与鼠标光标的最短距离**

```c#
DistanceToLine(Vector3 lineStart, Vector3 lineEnd)
```

 **在编辑器中进行对象的拾取**

根据鼠标光标位置获取场景中的对象

```c#
PickGameObject(Vector2 position, bool isSelecting)
```

## Gizmos公共类

Gizmos和Handles一样是用来让我们拓展Scene窗口的

而Gizmos相对Handles来说它主要专注于绘制辅助线、图标、形状等

而Handles主要用来绘制编辑器控制手柄等

### Gizmos响应函数

在**继承MonoBehaviour的脚本**中实现以下函数

便可以在其中使用Gizmos来进行图形图像的绘制

1. OnDrawGizmos() 在每帧调用，绘制的内容随时可以在Scene窗口中看见
2. OnDrawGizmosSelected() 仅当脚本依附的GameObject被选中时才会每帧调用绘制相关内容

它们的执行类似生命周期函数，Unity会帮助我们自动执行

```c#
private void OnDrawGizmos()
{
    Debug.Log("Gizmos");
}

private void OnDrawGizmosSelected()
{
    Debug.Log("Gizmos2");
}
```

### Gizmos API

**Gizmos修改颜色**

```c#
Gizmos.color = Color.green;
```

**Gizmos绘制基础形状**

```c#
//立方体
Gizmos.DrawCube(中心点, 大小);
Gizmos.DrawWireCube(中心点, 大小);
//球
Gizmos.DrawSphere(中心点, 半径);
Gizmos.DrawWireSphere(中心点, 半径);
//mesh网络
public Mesh mesh;
Gizmos.DrawWireMesh(mesh, 位置, 角度);
```

**Gizmos绘制视锥**

```c#
Gizmos.DrawFrustum(绘制中心, FOV(Field of View,视野)角度, 远裁切平面, 近裁切平面, 屏幕长宽比); 
```

**矩阵**

可以理解为是 绘制图形相对的坐标系

```c#
//修改Gizmos绘制前的矩阵
//Gizmos.matrix = Matrix4x4.TRS(位置, 角度, 缩放);
Gizmos.matrix = Matrix4x4.TRS(this.transform.position, this.transform.rotation, this.transform.localScale);//可以分别设置位置旋转缩放
Gizmos.matrix = this.transform.localToWorldMatrix;//等同于上一行代码
//还原矩阵
//Gizmos.matrix = Matrix4x4.identity
```

**绘制贴图**

```c#
Gizmos.DrawGUITexture(new Rect(x, y, w, h), 图片信息);
```

**绘制图标**

```c#
//图标需要放置在Assets/Gizmos/中
Gizmos.DrawIcon(Vector3.up, "图标名");
```

**绘制线段**

```c#
Gizmos.DrawLine(起点, 终点);
```

**绘制网格**

```c#
public Mesh mesh;
Gizmos.DrawMesh(mesh, this.transform.position, this.transform.rotation);
```

**绘制射线**

```c#
//位置 方向
Gizmos.DrawRay(this.transform.position, this.transform.forward);
```

