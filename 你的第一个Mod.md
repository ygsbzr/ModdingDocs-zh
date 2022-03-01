# 创造第一个Mod

## C#基础

了解C#
 - [Csharp 101 Video Series](https://docs.microsoft.com/en-us/shows/csharp-101/)
 - [A tour of the C# language Article Series](https://docs.microsoft.com/en-us/dotnet/csharp/tour-of-csharp)

非常适用于初学者！（无中文）

## mod基础结构

让我们看看一个最基础的Mod并分解它, 这是一个会在每帧当玩家按下O键时向我们的 `modlog.txt` 写日志的Mod

```cs 
using System;
using System.Collections;
using System.Collections.Generic;
using Modding;
using UnityEngine;

namespace MyFirstMod
{
    public class MyFirstMod: Mod 
    {
        new public string GetName() => "My First Mod";
        public override string GetVersion() => "v1";
        public override void Initialize()
        {
            ModHooks.HeroUpdateHook += OnHeroUpdate;
        }
        public void OnHeroUpdate()
        {
           if(Input.GetKeyDown(KeyCode.O))
           {
               Log("Key Pressed")
           }
        }
    }
}
```
这些都代表什么 ?

 - 最初的几行是 `using` 声明, 我们告诉C#编译器我们的文件将会使用哪些命名空间`（namespace）`的地方.
 - 然后, 我们创建一个叫`MyFirstMod` 的`namespace`，这个命名空间将会包含我们写的Mod
 -  `public class MyFirstMod : Mod`  我们定义了一个叫 `MyFirstMod`的公共类 ,它继承了 `Mod` 基类, 这是一个会被Modding api加载的类, 并且这个类是我们mod与游戏的交互处.
 - `new public string GetName() => "My First Mod";` Mod类中有一些特别的方法，我们可以用来干一些有趣的事情（夹带私货（bushi））例如 `GetName()` 允许我们设置游戏中显示的我们的Mod名,（左上角）
 -  `GetVersion` 与上面类似，但是是用于显示版本号(不写就是Unknown)(让我康康还有几个人以为这是报错).
 -  `Initialize` 在Api准备启动我们的Mod进行修改时调用, 这也是提供预加载的地方(稍后再谈)
 - `ModHooks.HeroUpdateHook` Modding Api提供给了我们一些我们可以作出反应的*事件*，这是允许我们可以写代码来对游戏事件进行响应的部分， 例如`HeroUpdateHook`就是一个在小骑士存在时每帧调用的事件, 这让我们监听按键并作出响应成为可能, 为了达到这个目的，我们使用自己的方法 `OnHeroUpdate`.
 - `OnHeroUpdate` 简单地检查 `O` 键在这一帧是否被按下, 如果按下就将字符串"Key Pressed"写入`Modlog.txt`

## Mod基类与生命周期 
为了深入了解Mod基类是什么以及mod的生命周期,你可以参考如下文档
 - [The Mod Class](mod-baseclass.md)
 - [The Mod Lifecycle](mod-lifecycle.md) 

## 安装Mod模板

严格来说，没有必要使用mod模板（某人从来不用）. 然鹅Mod之间有些东西是一样的，用模板就不用每次都写那些相同的东西（嗨！）.

安装模板步骤 : 
 - 下载 [Template Zip](https://cdn.discordapp.com/attachments/879130756146954240/931586813729075270/Hollow_Knight_1.5_Mod.zip) 
 - 将这个zip放入 `{documents folder}\Visual Studio 2022\Templates\ProjectTemplates`
 - 重启VS 

## 使用Mod模板创建Mod

 - 修正你mod项目里的程序集引用
 - 在一个叫`MyFirstMod.cs`的新文件中添加从[mod基础结构](#mod基础结构) 部分抄来的代码
 - 创建一个Build来编译你的第一个Mod!

## 将mod载入游戏的时间

在build成功时，mod模板会自动复制你mod的`dll` 进正确的文件夹（游戏不会启动）， 所有你要做的事情就是启动游戏，看见它在主菜单左上角有显示

如果你进了存档并按下了 `O` 你的 `modlog.txt` 里现在将会有字符串 `Key Pressed`. 你可以点击 [这里](logging.md#finding-your-modlog)来找modlog的路径

>TODO add instructions to enable modlog console

