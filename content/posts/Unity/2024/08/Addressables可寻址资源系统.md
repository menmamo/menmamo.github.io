---
title: "Addressables可寻址资源系统"
author: "mona"
date: 2024-08-30T10:51:00+08:00
categories: ["Unity"]
tags: ["资源系统"]
---

# 创建Addressables包

## 导入Addressables包

1. 打开Window——>Package Manager窗口
2. 设置包为Unity Registry
3. 找到Addressables包 进行安装

## 让资源变为可寻址资源

方法一：选中资源，勾选Inspector窗口中的Addressable

方法二：选中资源，拖入Addressables Groups窗口中

注意：

1. C#代码无法作为可寻址资源
2. Resources文件夹下资源如果变为寻址资源，会移入Resources_moved文件夹中

 原因：Resources文件夹下资源会最终打包出去，如果变为可寻址资源意味着想通过Addressables进行管理

 那么它就没有必要通过Resources方式去加载和打包，所以会自动迁移，避免重复打包，浪费空间

## 右键选择资源时菜单内容

- Move Addressables to Group：将该资源放入到现有的另一个组中
- Move Addressables to New Gourp：使用与当前组相同设置创建一个新租，并将该资源放入该新组中
- Rmove Addressables：移除资源，该资源会变为不可寻址资源
- Simplify Addressable Names：简化可寻址资源名，会删除名称中的路径和拓展，简化缩短名称
- Copy Address to Clipboard：将地址复制到剪贴板
- Change Address：改名
- Create New Group：创建新租

## 资源组窗口

### 资源信息

1. GroupName\Addressable Name：分组名\可寻址名（可重名，描述资源）
2. Path：路径（不可重复，资源定位）
3. .Labels：标签（可重复、可用于区分资源种类，例如青铜装备、黄金装备）

### 创建分组相关

Create——>Group

Packed Assets:打包资源分组

Blank(no schema):空白（无架构）

区别：Packed Assets默认自带默认打包加载相关设置信息，Blank没有相关信息需要自己关联

组对于我们来说意义重大，之后在资源打包时，一个组可以作为一个或多个AB包

### 选中某一组后右键

- Remove Group(s):移除组，组中所有资源恢复为不可寻址资源
- Simplify Addressable Names:简化可寻址名称，会删除名称中的路径和拓展，简化缩短名称
- Set as Default:设置为默认组，当直接勾选资源中的Addressable时，会自动加入该组
- Inspect Group Setting:快速选中关联的组相关配置文件
- Rename:重命名
- Create New Group:创建新组

### 配置概述相关

Manage Profiles：管理配置文件

可以配置打包目标、本地远程的打包加载路径等等信息

### Tools工具相关

- Inspect System Settings：检查系统设置
- Check for content Update Restrictions:检查内容更新限制
- Window：打开Addressables相关窗口
- Groups View：分组视图相关
- Show Sprite and Subobject Addressable：显示可寻址对象的精灵和子对象，一般想要看到图集资源内内容时可以勾选该选项
- Group Hierarchy with Dashes：带破折号的组层次结构

### Play Mode Script播放模式脚本（编辑模式下如何运行）

确定在编辑器播放模式下运行游戏时，可寻址系统如何访问可寻址资源

**Use Asset Database（fastest）：**

使用资源数据库（最快的），一般在开发阶段使用，使用此选项时，您不必打包可寻址内容，它会直接使用文件夹中的资源

在实际开发时，可以不使用这种模式，这种模式没有测试的意义

### Simulate Groups（advanced）：

模拟组（后期），一般在测试阶段使用，分析布局和依赖项的内容，而不创建AB包

通过ResourceManager从资产数据库加载资产，就像通过AB包加载一样

通过引入时间延迟，模拟远程资产绑定的下载速度和本地绑定的文件加载速度

在开发阶段可以使用这个模式来进行资源加载

### Use Existing Build（requires built groups）：

正儿八经的从AB包加载资源

使用现有AB包（需要构建AB包），一般在最终发布测试阶段使用

从早期内容版本创建的AB包加载资产

在使用此选项之前，必须使用生成脚本（如默认生成脚本）打包资源

远程内容必须托管在用于生成内容的配置文件的RemoteLoadPath上

### Build（构建打包相关）

- New Build：构建AB包资源（相当于打包资源分组）
- Update a Previour Build：更新以前的版本
- Clean Build：清空之前的构建资源

### 资源名注意事项

1. 资源路径一定不允许相同（后缀不同，名字相同可以）
2. 资源名可以随意修改
3. 之后在加载资源时我们可以使用名字和标签作为双标识加载指定资源

### 资源分组

我们可以按规则将资源进行分组

比如：角色、装备、怪物、UI等等

# 代码相关

## Addressables中的资源标识类

命名空间：using UnityEngine.AddressableAssets;

- AssetReference通用资源标识类 可以用来加载任意类型资源
- AssetReferenceAtlasedSprite图集资源标识类
- AssetReferenceGameObject游戏对象资源标识类
- AssetReferenceSprite 精灵图片资源标识类
- AssetReferenceTexture（2D/3D）贴图资源标识类
- AssetReferenceT<>指定类型标识类

通过不同类型标识类对象的申明 我们可以在Inspector窗口中筛选关联的资源对象

### 加载资源

所有Addressables加载相关都使用异步加载

通过事件函数传入的参数判断加载是否成功 并且创建

不会经常使用这种方式加载资源

```c#
public AssetReference assetReference;
assetReference.LoadAssetAsync<GameObject>().Completed += (handle) =>
{
    //使用传入的参数（建议）
    //判断是否加载成功
    if (handle.Status == AsyncOperationStatus.Succeeded)
    {
        GameObject cube = Instantiate(handle.Result);
        //一定资源加载过后 使用完后 再去释放
        assetReference.ReleaseAsset();
        materialRed.LoadAssetAsync().Completed += (obj) =>
        {
            cube.GetComponent<MeshRenderer>().material = obj.Result;
            //这样会造成使用这个资源的对象 资源丢失
            materialRed.ReleaseAsset();

            //这个异步加载传入对象的资源
            print(obj.Result);
            //这个是 资源标识类的资源
            print(materialRed.Asset);
        };
    }
    //使用标识类创建
    //if(assetReference.IsDone)
    //{
    //    Instantiate(assetReference.Asset);
    //}
}
```

**加载场景**

```c#
//加载完成后会直接切换场景
public AssetReference sceneReference;
sceneReference.LoadSceneAsync().Completed += (handle) =>
{
    //初始化场景的一些信息
    print("场景加载结束");
};
```

### 释放资源

**释放资源相关API：**

```c#
assetReference.ReleaseAsset();
```

1. 释放资源方法后,资源标识类中的资源会置空，但是AsyncOperationHandle类中的对象不为空
2. 释放资源不会影响场景中被实例化出来的对象，但是会影响使用的资源

### 直接实例化对象

```c#
 public AssetReferenceGameObject gameobjcetReference;
 gameobjcetReference.InstantiateAsync();
```

### 自定义标识类

自定义类 继承AssetReferenceT\<Material\>类 即可自定义一个指定类型的标识类

该功能主要用于Unity2020.1之前，因为之前的版本不能直接使用AssetReferenceT泛型字段

## Label标签

相同作用的不同资源（模型、贴图、材质、UI等等）

我们可以让他们的资源名相同

通过标签Label区分他们来加载使用

**通过标签相关特性约束标识类对象**

```c#
[AssetReferenceUILabelRestriction("SD")]
public AssetReference assetReference;
```

## 资源动态加载

### 动态加载单个资源

1. 如果存在同名或同标签的同类型资源，我们无法确定加载的哪一个，它会自动加载找到的第一个满足条件的对象
2. 如果存在同名或同标签的不同类型资源，我们可以根据泛型类型来决定加载哪一个

```c#
AsyncOperationHandle<GameObject> handle;
handle = Addressables.LoadAssetAsync<GameObject>("Cube");
handle.Completed += (handle) =>
{
    //判断加载成功
    if (handle.Status == AsyncOperationStatus.Succeeded)
        Instantiate(handle.Result);

    //一定要是 加载完成后 使用完毕后 再去释放
    //不管任何资源 只要释放后 都会影响之前在使用该资源的对象
    Addressables.Release(handle);//释放资源
};
```

### 动态加载多个资源

```c#
//根据资源名或标签名加载多个对象
//参数一：资源名或标签名 
//参数二：加载结束后的回调函数
//参数三：如果为true表示当资源加载失败时，会自动将已加载的资源和依赖都释放掉；如果为false，需要自己手动来管理释放
AsyncOperationHandle<IList<Object>> handle = Addressables.LoadAssetsAsync<Object>("Red", (obj) =>
{
    //print(obj.name);
});
 //注意：我们还是可以通过泛型类型，来筛选资源类型
```

### 根据多种信息加载多个对象

```c#
//参数一：想要加载资源的条件列表（资源名、Lable名）
//参数二：每个加载资源结束后会调用的函数，会把加载到的资源传入该函数中
//参数三：可寻址的合并模式，用于合并请求结果的选项。
    //如果键（Cube，Red）映射到结果（[1,2,3]，[1,3,4]），数字代表不同的资源
    //None：不发生合并，将使用第一组结果 结果为[1,2,3]
    //UseFirst：应用第一组结果 结果为[1,2,3]
    //Union：合并所有结果 结果为[1,2,3,4]
    //Intersection：使用相交结果 结果为[1,3]
//参数四：如果为true表示当资源加载失败时，会自动将已加载的资源和依赖都释放掉
//      如果为false，需要自己手动来管理释放
List<string> strs = new List<string>() { "Cube", "HD" };
Addressables.LoadAssetsAsync<Object>(strs, (obj) => {
    print(obj.name);
}, Addressables.MergeMode.Intersection);
 //注意：我们还是可以通过泛型类型，来筛选资源类型
```

### 动态加载场景

- 参数一：场景名
- 参数二：加载模式 （叠加还是单独,叠加就是两个场景一起显示,单独就是只保留新加载的场景，正常情况为单独）
- 参数三：场景加载是否激活，如果为false，加载完成后不会直接切换，需要自己使用返回值中的ActivateAsync方法
- 参数四：场景加载的异步操作优先级

```c#
Addressables.LoadSceneAsync("SampleScene", UnityEngine.SceneManagement.LoadSceneMode.Single, false).Completed += (obj)=> 
{
    //手动激活场景
    obj.Result.ActivateAsync().completed += (a) =>
    {
        //注意：
        //场景资源也是可以释放的，并不会影响当前已经加载出来的场景，因为场景的本质只是配置文件
        Addressables.Release(obj);
    };
}
```

# 配置文件

## Profiles概述配置

主要用于配置Addressable打包（构建）加载AB包时使用的一些变量

这些变量定义了

1. 在哪里保存打包（构建）的AB包
2. 运行时在哪里加载AB包

可以添加自定义变量，以便在打包加载时使用

### 打开Profiles窗口

方法一：Window > Asset Management > Addressables > Profiles

方法二：在AddressableAssetSettings中打开

方法三：在Addressables Groups窗口中打开

### Profiles变量语法

所有的变量类型都是string字符串类型

可以在其中填写一些固定的路径或值来决定路径

还可以使用两个语法指示符让原本的静态属性变成动态属性

- []:方括号，可以使用它包裹变量，在打包构建时会计算方括号包围的内容

  比如

  使用自己的变量[BuildTarget]

  使用别的脚本中变量[UnityEditor.EditorUserBuildSettings.activeBuildTarget]

  在打包构建时，会使用方括号内变量对应的字符串拼接到目录中

- {}:大括号，可以使用它包裹变量，在运行时会计算大括号包围的内容

  比如

  使用别的脚本中变量{UnityEngine.AddressableAssets.Addressables.RuntimePath}

## 配置文件有哪些

在导入Addressables包之后 创建的那些就是配置文件

AddressableAssetsData文件夹下的内容都是本质为ScriptableObject的数据配置文件

- AddressableAssetsData（可寻址资源数据）
- AssetGroups(资源组)--当我们创建一个组就会多一些相关数据配置文件
- AssetGroupTemplates（资源组模板，主要是对资源组的一些默认设置）
- Packed Assets:打包资源数据配置
- DataBuilders（数据生成器，这些内容决定了在不同模式下，资源打包和使用的方式）
  - BuildScriptFastMode:构建脚本快速模式
  - -BuildScriptPackedMode:构建脚本打包模式
  - BuildScriptPackedPlayMode:构建脚本打包播放模式
  - BuildScriptVirtualMode:构建脚本虚拟模式
- AddressableAssetSettings（可寻址资源设置）
- DefaultObject（默认对象）

## AddressableAssetSettings 可寻址资源数据设置

**Profiles（概述配置）**

- Profile In Use：可以在这选择使用的是哪一套配置文件
- Manage Profiles：点击它会打开管理配置文件窗口

**Diagnostics:诊断**

- Send Profiler Events：启用分析器事件，启用它后我们可以在Event Viewer窗口查看Addressable相关信息
- Log Runtime Exceptions：记录运行时加载相关的异常

**Catalog目录相关设置**

将资源的地址映射到其物理位置

- Player Version Override：重写用于制定远程目录名称的时间戳
  如果不设置默认使用时间戳作为远程目录命名
- Compress Local Catalog：在压缩的AssetBundle文件中生成目录。可以压缩大小，但是会增加生成和加载的时间
- Optimize Catalog Size：通过为内部ID创建查找表来减小目录的大小。会增加加载目录所需的时间
- Build & Load Paths：
  在何处生成和加载远程目录。从列表中选择一个配置文件路径，如果要分别设置生成路径和加载路径，请选择\<custom\>。
  仅在启用生成远程目录时可见。
  - Build Path：远程构建路径，在何处构建远程目录。通常，应该使用RemoteBuildPath配置文件变量。
    仅当将生成和加载路径设置为\<custom\>时显示。
  - Load Path：远程加载路径，用于访问远程目录的URL。通常，应该使用RemoteLoadPath配置文件变量。
  - 仅当将生成和加载路径设置为\<custom\>时显示。
  - Path Preview：路径预览

**Downloads**

- Custom Certificeate handle：用于自定义证书处理的类。该列表包含项目中扩展UnityEngine的所有类。网络。证书管理员。
- Max Concurrent Web Requests：系统对超过此限制的任何请求进行队列处理
- Catalog Download Timeout：等待目录文件下载的时间为多少秒。

**Build 生成构建相关设置**

- Ignore Invalid/Unsupported Files in Build：忽略生成中无效不受支持的文件，如果启用，Addressable生成脚本将排除无效或不受支持的文件，而不是中止生成
- Unique Bundle IDs：唯一约束ID，是否为每个构建中的包生成一个唯一的名称
- Contiguous Bundles：连续捆绑，生成更高效的捆绑包布局。如果您有Addressables 1.12.1或更早版本生成的捆绑包，请禁用此选项以最小化捆绑包更改
- Non-Recursive Dependency Calculation：非递归依赖计算，不使用递归计算依赖项
- Shader Bundle Naming Prefix：着色器包命名前缀
- MonoScript Bundle Naming Prefix：Mono脚本包命名前缀
- Strip Unity Version From AssetBundles：从AssetBUndles中剥离Unity版本，决定是否从包头中删除版本信息
- Disable Visible Sub Asset Representations：禁用可见子资源，如果您不直接使用子对象(Sprite、子网格等)，则启用此选项可以提高构建时间

**Build and Play Mode Scripts构建和编辑器模式脚本**

在编辑器中进入播放模式时，选择Addressable系统如何加载资产
这些脚本处理默认的构建进程，并提供不同的方式在编辑模式下访问数据
我们可以在AddressableAssetData/DataBuilders文件夹中找到这些脚本
如果要为他们自定义脚本，那么对应的脚本需要时BuildScriptBase的子类，并且继承IDataBuilder

- Use Asset Database：使用资源数据库
- Simulate Groups：模拟组
- Use Existing Build：使用现有版本
- Default Build Scripts:默认生成脚本

**Asset Group Templates资源组模板**

- Packed Asset：打包资源
  定义可用于创建新组的模板列表
  创建新模板时，必须先将其添加到此列表中，然后才能使用它

Addressables包包含一个模板
其中包含默认构建脚本使用的模式。您可以在AddressableAssetData/AssetGroupTemplates文件夹中找到该模板。

**Initalization Objects 初始化对象列表**

配置初始化对象

初始化对象列表
配置初始化对象

可以在Project右键Create>Addressables>Initialization(初始化)>Cache Initialization Setting（缓存初始化设置）

## Packed Assets 打包资源数据配置

Packed Assets 翻译过来的意思是 打包资产（资源）

它的作用是确定如何处理组中的资源

比如：你可以指定生成AB包的位置和包压缩相关的等等设置

**创建自定义的配置：**

在Project窗口右键或者点击+号

Create(创建)——>Addressables(可寻址)——>Group Templates(组模板)——>Blank Group Template(空白组模板)

**参数：**

**Content Packing & Loading**

- Build & Load Paths：配置文件路径对，定义可寻址构建系统在哪个路径为此组创建内容，以及可寻址系统在运行时在何处加载这些内容
- Build Path：打包路径
  - LocalBuildPath-本地路径
  - RemoteBuildPath-远程路径
- Load Path：加载路径
  - LocalLoadPath-本地路径
  - RemoteLoadPath-远程路径
- Path Preview：路径预览

**Advanced Options高级选项**

- Asset Bundle Compression：AB包的压缩方式，默认为LZ4，它通常是最有效的选项，建议使用
  - Uncompressed：不压缩，包较大，不推荐
  - LZ4：压缩，相对LZMA大一点点，建议使用，用什么解压什么，内存占用低
  - LZMA：压缩最小，解压慢，用一个资源要解压所有
- Include In Build：包含在构建中，是否在内容生成中包含此组中的资源。如果取消勾选，那么在选择打包时，不会打包该组内容
- Force Unique Provider：强制唯一提供者，Addressable是否对此组使用资源提供程序类的唯一实例。如果您有此组中的资产类型的自定义提供程序实现，并且这些提供程序的实例不能在组之间共享，则启用此选项。
- Use Asset Bundle Cache：使用AB包缓存，是否缓存远程分发的包
- Asset Bundle CRC：是否在加载前验证AB包的完整性
  - Disabled：永远不检查完整性
  - Enabled，Including Cached：检查完整性，包括缓存也检查
  - Enabled，Excluding Cached：检查完整性，但是不检查缓存的包
- Use UnityWebRequest for Local Asset Bundle：
  加载AB包时，使用UnityWebRequestAssetBundle.GetAssetBundle而不是AssetBundle.LoadFromFileAsync
- Request Timeout：下载远程包时超时的间隔时间
- Use Http Chunked Transfer：下载包时是否使用HTTP/1.1块传输编码方法。
  在2019.3+中被废弃和忽略
- Http Redirect Limit：下载包时允许重定向的次数。无限制设置为-1
- Retry Count：重试失败下载的次数
- Include Addresses in Catalog：是否将地址字符串包括在目录中。如果不使用地址字符串在组中加载资产，则可以通过不包括它们来减小目录的大小。
- Include GUIDs in Catalog：是否在目录中包含GUID字符串。您必须包含guid字符串才能使用资产参考。如果不使用AssetReferences或GUID字符串在组中加载资产，则可以通过不包括它们来减小目录的大小。
- Include Labels in Catalog：是否在目录中包含标签字符串。如果不使用标签在组中加载资产，则可以通过不包括这些资产来缩小目录的大小。
- Internal Asset Naming Mode：如何在内部命名目录中的资源
  - Full Path：全路径
  - FileName：文件名
  - GUID：资源的Guid字符串
  - Dynamic：Addressables根据组中的资源选择最小的内部名称
- Internal Bundle Id Mode	：确定如何构造资产绑定的内部ID。例如，当您设置群GUID选项，Addressables通过将组名与包guid字符串组合来创建包ID。
  - Group Guid：组的Guid
  - Group Guid Project Id Hash：组的ID和工程ID的哈希
  - Group Guid Project Id Entries Hash：组的ID和工程ID项的哈希
- Cache Clear Behavior：确定安装的应用程序何时从缓存中清除AB包
  - Clear When Space Is Needed In Cache：在缓存中需要空间时清除
  - Clear When When new Version Loaded：加载新版本时清楚
- Bundle Mode：打包模式，如何将此组中的资产打包到包中
  - Pack Together：创建包含所有资产的单个包
  - Pack Separately：为组中的每个主要资产创建一个包。如精灵图片中的精灵图片被包装在一起。添加到组中的文件夹中的资产也打包在一起
  - Pack Together by Label：为共享相同标签组合的资产创建一个包
- Bundle Naming Mode：如何构造AB包的文件名
  - Filename：文件名
  - Append Hash to Filename：将哈希附加到文件名
  - Use Hash of AssetBundle：使用AB包的哈希
  - Use Hash of FileName：使用文件名的哈希
- Asset Load Mode：资源加载模式
  - Requested Asset And Dependencies：请求的资源和依赖项
  - All Packed Assets And Dependencies：所有包中的资源和依赖项
- Asset Provider：资源提供者
  定义提供程序类Addressable用于从该组生成的AssetBundles加载资产。将此选项设置为Bundles供应商的资产除非您有一个自定义提供程序实现来从资产绑定中提供资产。
- Asset Bundle Provider：AB包提供器
  定义由该组生成的提供程序类Addressable用于加载AssetBundles。将此选项设置为资产束提供者除非您有一个自定义提供程序实现来提供资产绑定。

**Can Change Post Release 内容更新限制**

- Can Change Post Release：可以改变发行后内容，该模式不移动任何资源，如果包中的任何资源发生了更改，则重新构建整个包
- Cannot Change Post Release：无法改变发布后内容，如果包中任何资源已经改变，则[检查内容更新限制]工具会将其移动到为更新创建的新组中。在进行更新构建时，从这个新组创建的AssetBundles中的资产将覆盖现有包中的版本。

**Add Schema（添加模式）**

你可以将任意数量的架构模式分配给一个组

- Content Packing Loading：内容打包加载相关
- Content Update Restriction：内容更新限制
- Resources and Built In Scenes：在内置数据中显示哪些类型的内置资源，可以选择是否显示 资源和内置场景

我们甚至可以通过继承AddressableAssetGroupSchema
定义自己的架构模式

##  Addressables Hosting可寻址托管窗口

一般资源服务器需要将其搭建为http服务器

这样才能进行资源的上传和下载

而Unity为了简化本地测试的这一过程

提供了快捷搭建http服务器的工具

 Addressables Hosting 窗口

通过它我们可以将我们的本机模拟为一台远端服务器来进行远端发布加载测试

**打开可寻址托管窗口：**

方法一：Window > Asset Management > Addressables > Hosting

方法二：Addressables Groups窗口中 > Tools > Window > Hosting Services

**可寻址托管窗口参数**

- Service Name：服务器名称
- Service Type（ID）：服务器类型
- Port：端口号（一般不自己设置，避免和其它应用产生冲突）
- Upload Speed（kb/s）：上载速度
- Enable：是否启用，启用后会自动分配端口号
- Hosting Service Variables：托管服务变量
- Variable Name：变量名
- Value：值
  我们可以使用这些变量名来编辑Addressables Profiles
  中的发布和加载相关路径
  http://[PrivateIpAddress]:[HostingServicePort]/[BuildTarget]

# 资源打包（发布）加载

## 资源打包是指什么？

资源打包是指，将可寻址资源打包到AssetBundle资源绑定包中

使用AssetBundle时需要我们自己写代码或者通过工具进行打包

现在Addressable将这个过程自动化了

可寻址资源中资源一般以组为单位进行打包

最终的AB包数量，都是基于资源组决定的

打包好的AB包，我们可以自定义如何使用它们

1. 发布游戏时所有AB包作为原始资源一起打包出去（这样做的目的仅仅是单机游戏为了减小游戏包体）
2. 将AB包放到远端服务器，发布游戏时只打包必备资源（这样做的目的是不仅可以减小包体，还可以进行热更新）

## 资源打包的模式

Packed Assets配置相关中有一个打包模式的字段Bundle Mode

有三种打包的方式

- Pack Together：创建包含组中所有资产的单个包
- Pack Separately：为组中的每个类型的资源创建一个包。如精灵图片中的精灵图片被包装在一起
- Pack Together by Label：为共享相同标签组合的资产创建一个包

一般情况下，我们都使用第一种模式，按组来进行打包，所以在整理资源时要尽量合理

## 资源打包的注意事项

1. 场景资源始终有自己单独的包

   当一个可寻址包中有场景资源和普通资源时，场景资源和其它资源始终会被分开打包

   也就是说，该组会生成两个包，一个场景的，一个其它资源的‘

2. 资源依赖的注意事项

如果资源a和资源b都使用了资源c，但是资源a和b是可寻址资源但不在一个组中，而c不是可寻址资源

那么这时资源c分别会被打进a和b的包，相当于这时的c就被重复利用了，浪费了空间

较好的解决方案是:将c也作为可寻址资源， a-A包  b-B包  c-C包

这时c不会被打进A、B包，A、B包只会依赖于C，而Addressable会自动帮助我们处理依赖问题

**注意：**在使用图集时，尤为要注意这个问题，当不同包中内容使用同一个图集中图片时，

建议将图集也作为可寻址资源，并且有专门的一个图集包
让其它使用图集中图片的对象会对该图集包产生依赖

## 合理安排可寻址资源分组

同类型，同作用放一起

比如：角色组、怪物组、武器组、衣服组、登录UI组、装备UI组、音效组、可变贴图组、图集组等等

关于包的数量（分组的数量）

要根据实际情况来对资源进行布局

包(分组)过多、包(分组)过大都不太好

## 编辑器中资源加载的几种方式

- Use Asset Database（fastest）：使用资源数据库（最快的）

  不用打AB包，直接本地加载资源，主要用于开发功能时

- Simulate Groups（advanced）：模拟组（后期）

  不用打AB包，通过ResourceManager从资产数据库加载资产

  引入时间延迟，模拟远程AB包的下载速度和本地AB包加载速度

  开发阶段可以使用这个模式来进行资源加载

- Use Existing Build（requires built groups）：使用AB包加载资源

  必须打AB包后使用会从AB包中加载资源

## 本地发布

所有组的加载和发布都选择本地路径

LocalBuildPath-打包路径

LocalLoadPath-加载路径

注意：使用默认设置，当发布应用程序时，会自动帮我们将AB包放入StreamingAssets文件夹中

如果修改了路径，我们需要自己将内容放入StreamingAssets文件夹中

不建议修改默认的本地构建和加载路径

## 远端发布资源

**模拟远端发布资源**

1. 将本机模拟为一台资源服务器，通过Unity自带工具或者第三方工具
2. 设置远端路径
3. 打包

 **实际上的远端发布资源**

1. 在远端的电脑上搭建Http服务器
2. 将打包出来的资源上传到对应服务器上
3. 设置远端路径
4. 打包

## 资源更新

当项目正式发布后，对于远程加载的资源

我们可以通过改变资源服务器上的AB包内容来达到更新游戏的目的

Addressables会自动帮助我们判断哪些资源更新了，并加载最新的内容

在组设置中有一个内容更新限制的设置Content Update Restriction

Can Change Post Release：

可以改变发行后内容，该模式不移动任何资源，如果包中的任何资源发生了更改，则重新构建整个包

Cannot Change Post Release：

无法改变发布后内容，如果包中任何资源已经改变，

则[检查内容更新限制]工具会将其移动到为更新创建的新组中。

在进行更新构建时，从这个新组创建的AssetBundles中的资产将覆盖现有包中的版本。

### 整包更新

组设置为 Can Change Post Release

整包更新指，某一个分组的资源发生变化后

我们需要将其整体进行打包

这种方式比较适用于大范围资源更新时使用

坏处是，玩家需要下载的内容较大，比较耗时耗流量

```c#
Addressables.LoadAssetsAsync<GameObject>(new List<string>() { "Cube", "SD" }, (obj) =>
{
    Instantiate(obj);
}, Addressables.MergeMode.Intersection);
```

注意：Unity自带的 资源服务器模拟工具有问题 有的时候明明开启了服务 但是加载不成功

可以使用第三方工具来搭建我们的模拟本地资源服务器

### 局部更新

组设置为 Cannot Change Post Release

局部更新指，当组中有资源发生变化时

我们可以单为发生变化的内容生成AB包

之后使用该资源时，Addressables会自动加载最新的内容

它相对整包更新来说，更节约时间和流量

## 事件查看窗口

使用可寻址事件查看窗口可以监视可寻址资源的资源内存管理

1. 显示应用程序何时加载和卸载资源
2. 显示所有可寻址系统操作的引用计数
3. 显示应用程序帧率和分配的内存总量近似图

### 打开事件查看窗口

注意：使用事件查看窗口的前提要打开AddressablesAssetSettings配置文件中的事件发送开关

1. Window > Asset Management > Addressables > Event Viewer
2. Addressabeles Groups > Window > Event Viewer

###  事件查看窗口使用

- 左上角：
  - Clear Event：清楚所有记录的帧，会清空窗口中所有内容
  - Unhide All Hidden Events：显示你隐藏的所有事件内容（当我们右键一个内容隐藏后才会显示该选项）
- 右上键：
  - Frame：显示当前所在帧数
  - 左按钮和右按钮：在记录的帧中前后切换查看信息
  - Current:选中当前帧
- 中央部分：
  - FPS:应用的帧率
  - MonoHeap：正在使用的托管堆内存量
  - Event Counts：事件计数，某一帧中发生的可寻址事件的数量
  - Instantiation Counts:实例化计数，某一帧中Addressables.InstantiateAsync的调用数量
  - 线性图标：显示统计的什么时候加载释放资源的信息

Event 相关：显示当前帧中发生的可寻址操作的事件

## 构建布局报告

构建布局报告提供了有关可寻址资源的构建打包的详细信息和统计信息

1. AB包的描述
2. 每个资源和AB包的大小
3. 解析作为依赖项隐式包含在AB包中的不可寻址资源
4. AB包的依赖关系

### 如何查看构建布局报告

1. 启用调试构建布局功能
2. Edit > Preferences > Addressables
3. 启用Debug Build Layout

只要我们构建打包可寻址资源后

就可以在Library/com.unity.addressables/文件夹中找到buildlayout.txt文件

## 用多包策略还是大包策略？

AB包太多(太小)的问题

- 每个包都会有内存开销，如果加载过多的包可能会带来更多的内存消耗
- 并发下载的问题，如果包小而多，以为着下载包时可能需要更多的时间
- 目录文件会因为过多的包而变大，因为它要记录更多的包信息
- 复用资源的可能性更大，比如多个包使用同样一个资源，但是该资源不是寻址资源，那么在每个包中都会有该资源

AB包太少(太大)的问题

- 过大的包如果下载失败，下次会重新下载，因为使用UnityWebRequest下载包时不会恢复失败的下载
- 能单独加载，但是不能单独卸载，更大的包意味着包中有更多资源，比如加载了1个大包中100个资源

## 哪种压缩方式更好？

AB包的压缩方式：不压缩、LZ4、LZMA

一般情况LZ4用于本地资源，LZMA用于远端资源

主要原因是LZMA的压缩内容更小，更节约下载时间和流量

注意：压缩不会影响加载内存的大小，只会影响包体大小，下载时间等

但是也要根据实际情况

1. 不压缩：包体并不大的单机游戏，使用不压缩最好，没有包体大小的压力，加载也是最快的，因为不用解压
2. LZ4：它是基于块的压缩，所以提供了加载文件的能力，加载资源时不用全加载AB包，只加载使用的内容，相对LZMA来说更节约内存
3. LZMA：不建议用它在本地内容中，因为它虽然包最小，但是加载最慢，用它只是为了节约下载时间和极限压缩包体大小

## 减小目录文件大小

当我们想要极限压缩包体大小时，可能希望优化目录文件的大小

1. 压缩本地目录

   AddressableAssetSettings > Catalog > Compress Local Catalog

2. 禁用内置场景和资源

   默认Addressables提供了从Resources等内置资源文件夹中加载资源以及加载内置场景

   如果你不通过Addressables加载他们，可以禁用，这样目录文件就不会包含其中信息

   但是我们就只能使用老方法加载同名

   个人建议取消，因为一般我们不会通过Addressables去加载非寻址资源

## 注意事项

1. 关于AB包最大的限制，老版本不支持大于4G的包，虽然新版本中已经没有这个限制

   但是为了兼容性，还是建议大小控制在4G以下

2. 活用可寻址资源上的Groups View中的两个功能

   Show Sprite and Subobject Addresses:当窗口中内容特别多时，禁用它可以提升窗口加载的性能

   Group Hierarchy with Dashes：启用带破折号的层级结构 可以让我们在内容特别多时以层级结构查看分组信息

