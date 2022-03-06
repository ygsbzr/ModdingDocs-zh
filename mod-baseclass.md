# The Mod base class

## Introduction

The Mod base class is the class that allows the Modloader (Modding Api) to load your mod into the game, as such, the class deriving from this class is the one that will serve as the entry point of your mod, where you can : 

 - [Set your load priority](#set-your-load-priority)
 - [Set your Mod's Name](#set-your-mods-name)
 - [Set your Mod's Version](#set-your-mods-version)
 - [Request for preloads](#request-for-preloads)
 - [Initialize your mod](#initialize-your-mod)
 
 you can also use additional features of the base class, by implementing optional interfaces
 
  - [Make your mod toggleable ](#make-your-mod-toggleable)
  - [Add Global Settings](#add-global-settings)
  - [Add Save Settings](#add-local-settings)
  - [Add a Mod Menu](#add-a-mod-menu)
 
 ### Set your load priority
By default Mods are loaded in an unspecified order, if for some reason you need to ensure that your mod loads earlier or later than some other mods, you can use the load priority to denote that. 

To do this add the following method to your Mod class :

``` cs
public override int LoadPriority() => PRIORITY;
```

Where `PRIORITY` is an int, higher values of priority will load your mod later and lower values will load your mod earlier in relation to other mods. i.e any Mod with priority 5 loads after any Mods priority 4 or lower.

>Note: if no priority is specified, then the default priority of `0` is used

By "load" order we strictly mean the order in which the `Initialize` method is called, the Mod class constructors may be called out of this order, do not rely on constructor order.


 ### Set your Mods Name
 The Default name of your mod is the name of the Mod class, if you need to change that, you can do so by passing the name to the base class constructor, like so :
 
``` cs 
public MyFirstMod() : base("My 1st Mod") { }
```

 ###  设置mod版本号
 让使用者可以知道使用的版本以便于调试等工作
 
``` cs 
 public override string GetVersion() => "v0.1";
```

> 注意:建议在你每发布一个新版本前都修改版本号以便于其他人可以在下载/报bug时使用正确的版本

 ### 预加载需求
 
mod基类允许你向api发送请求以预加载来自某一个房间的游戏物品
获取更多信息请看  [预加载游戏物品](preloads.md) .

 ### mod初始化
mod类允许你的mod去 [接收预加载物品](preloads.md), 并应用于mod初始化阶段. 这个方法在你的mod构建完成后调用,但要注意它调用的次数不止一次, 特别是你的mod开关的时候. 查阅 [Mod Lifecycle（mod生命周期）](mod-lifecycle.md) 以获取更多细节.

## 可选择的接口

### 让你的mod可以开关

>即将呈现

### 添加global setting

Global setting是mod可以保存mod设置的方法 (**这种设置在各个存档间是一样的**)

参考 [创建global settings](saving-mod-data.md#creating-global-settings)

### 添加 local settings

Local settings 是在每个存档之间保存不同设置的方法, 这可能是设置，但对于更多涉及的mod，这可能包括保存玩家状态， 例如 : 如果一个玩家拿到了不属于原存档的自定义物品, 可以保存到localsetting.

参考 [创建 local settings](saving-mod-data.md#creating-local-settings)

### 添加mod菜单

Mod 菜单是mod提供给玩家的一种可交互ui来修改各种设置*特别是globalsetting和localsetting*，和手动修改setting文件的效果一样。 作为使用自定义ui来解决这个问题的替代品, 建议使用mod菜单mod菜单来让玩家有着一致的体验，并且这样做可以让暂停菜单不被弄乱.

获取更多有关如何打造一个mod菜单的信息, 请前往包含不同种类 [Mod 菜单](modmenu.md)的菜单页
