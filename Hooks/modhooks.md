---
title: ModHooks
nav-order: 1
parent: Hooks
---

# ModHooks
ModHooks are hooks that are built into the Modding API that allow us to interact will Hollow Knight code.
To use a Modhook, you need to subscribe to the hook. For example:
```cs
public class MyFirstMod:Mod
{
    // The method that will be called by Modding API when the game first opens
    public override void Initialize()
    {
        // We subscribe to HeroUpdateHook which is an hook that is triggered when the 'Update' Function is called for the player (once every frame)
        ModHooks.HeroUpdateHook += OnHeroUpdate;
    }
    
    //This event's method doesn't take any Parameters. normally the IDE can generate this function for you with the correct parameters
    public void OnHeroUpdate()
    {
       //code to run
    }
}
```
> Note: Your IDE (Visual Studio Community/Jetbrains Rider) can generate this function for you with the correct parameters. To do this, [see example video](https://youtu.be/oH-lbfZORw0) or type in `ModHooks.HeroUpdateHook += OnHeroUpdate;`, Then right click on the now red highlighted `OnHeroUpdate` and click on the light bulb icon (called 'Quick actions and Refactoring') and choose 'Generate Method'.


### HeroUpdateHook
这个事件将在 `HeroController.Update`函数中被调用.  
它可以用于玩家存在的每一帧.
在需要代码每帧运行而没有创建Monobehavior时非常有用.
可以用于检查各种情况以及跑代码 (示例：检测 `Input.GetKeyDown(KeyCode.Space))` （下面的代码用于检测按下空格键）.
```cs
ModHooks.HeroUpdateHook += HeroUpdate;
public string HeroUpdate()
{
    if Input.GetKeyDown(KeyCode.Space))
    {
        Log("Space was pressed");
    }
}
```

### LanguageGetHook
这个钩子是更改游戏内文本的主要方法. 这个方法将在每次游戏想要找到需要的文本时调用, 
它使用 `Language.Language.Get` 函数(LanguageHook调用处）来找到基于 "key"（关键词）的文本. 文本同时也被组织进了sheet(表单)中,所以我们可以使用key和sheet来确定唯一的文本,.看三者对应关系请参考 
[the full list of all keys and sheets](https://docs.google.com/spreadsheets/d/1_sQ5ygsrN42toz3VnKy-8v6bwrh0UoaV/edit?usp=sharing&ouid=105022867698529839659&rtpof=true&sd=true). 或者通过该钩子函数打log
该钩子的使用方法, 检查 `key` 和 `sheetTitle` ，然后如果是你想改的文本对应的key/sheetTitle，返回新文本
或者, 你可以使用 `orig` 来检查文本内容来返回新文本.然而，我们不推荐这么做，因为这样可能会导致未知的文本变化.
下面有个例子
```cs
ModHooks.LanguageGetHook += LanguageGet;
public string LanguageGet(string key, string sheetTitle, string orig)
{
    //检查文本的key和sheet
    if (key == "NAV_YES" && sheetTitle == "MainMenu")
    {
        //返回新文本 
        return "Yee";
    }
    
    //一种把Yes都替换为Yee的办法
    if (orig.Contains("Yes"))
    {
        return orig.Replace("Yes", "Yee");
    }
    
    //确保其他原文本正常
    return orig;
}
```

这个钩子也可以用于自定义key. (例如利用 SFCore 添加自定义护符). 做法：
 `if(key == "MyCustomKey") return "MyCustomText`.

| 参数类型        | 参数名          |注解                                                                                                                                   |
|----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------|
| string         | key            | 表单中文本的关键词. 它和SheetTitle结合来确定唯一文本                                                                                     |
| string         | sheetTitle     | 文本所在的表单                                                                                                                         |
| string         | orig           | 原文本. 在没有改变时记得返回                                                                                                            |

| 返回类型     | 注解                                                                          |
|-------------|-------------------------------------------------------------------------------|
| string      |  新文本（记得在无改动时返回原文本）                                              |

### BeforeSceneLoadHook
> 注意: 这个钩子由于以下原因平常不需要使用:
> 1. 它只允许你改动场景名，不允许改动其他数据 
> 2. 如果你的目的是查看新进的场景, 使用 `UnityEngine.SceneManagement.SceneManager.activeSceneChanged` 代替
> 3. 如果你的目的是查看进入了哪个场景并改动它, 用 `On.GameManager.BeginSceneTransition` 代替 (因为可以解决问题1). 

这个事件在 `GameManager.BeginSceneTransition`被调用. 它在新场景前加载调用， 允许你决定哪个场景被加载. 但不推荐使用 (看上面).
```cs
ModHooks.BeforeSceneLoadHook += BeforeSceneLoad;
public string BeforeSceneLoad(string newSceneName)
{
    Log($"new scene is {newSceneName}");
    return newSceneName;
}
```
| 参数类型 | 参数名 | 注解                                                   |
|----------------|----------------|----------------------------------------|
| string         | newSceneName   | 原场景名 |

| Return type | Explanation                                     |
|-------------|-------------------------------------------------|
| string      | 代替的场景名                                     |

### SavegameLoadHook and NewGameHook
尽管这俩玩意放一起可以检测打开存档, 最好避开使用这俩钩子因为
1. NewGameHook 这玩意在建立随机档时不触发
2. 这么做很蠢（

作为替代品我们推荐使用
1. `On.UIManager.StartNewGame` 检测新开的档
2. `On.HeroController.Awake` 检测打开存档(包括开档)

### FinishedLoadingModsHook
这个事件在所有mod加载完后触发. 在需要其他mod运行时很有用.
```cs
ModHooks.FinishedLoadingModsHook += FinishedLoadingMods;
public string FinishedLoadingMods()
{
    //检测DebugMod
    if (ModHooks.GetMod("DebugMod") is Mod)
    {
        Log("Debug Mod exists");
    }
}
```

### AfterTakeDamageHook
这个事件在 `HeroController.TakeDamage`中触发. 它在说明无敌后，伤害结算前调用. 可以改变受到的伤害
> Note: 不像1.4api, AfterTakeDamageHook 会在触发乌龟壳后调用并且damage为0
```cs
ModHooks.AfterTakeDamageHook += AfterTakeDamage;
public int AfterTakeDamage(int hazardType, int damageAmount)
{
    Log($"Damage amount is {damageAmount}");
    
    //make player take double damage
    return damageAmount * 2;
}
```
| Parameter type | Parameter name | Explanation                                                                                                                                                                      |
|----------------|----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| int            | hazardType     | 尽管传的是一个整数，实际上是枚举 `HazardType（伤害类型）`. 可能的数值有 => (0) `NON_HAZARD（例：碰怪）`,(1) `SPIKES（荆棘/刺）`,(2) `ACID（酸水）`,(3) `LAVA（？？？）`, (4) `PIT（虚空背刺.jpg）`, |
| int            | damageAmount   | 小骑士受到的伤害大小                                                                   |

| Return type | Explanation                                         |
|-------------|-----------------------------------------------------|
| int         | damageAmount |


### BeforeAddHealthHook
这个事件在 `PlayerData.AddHealth`中被触发. 它在普通加血（聚集）时触发. 可以用于改变加血的量
```cs
ModHooks.BeforeAddHealthHook += BeforeAddHealth;
public int BeforeAddHealth(int amount)
{
    Log("Amount of health to be healed is {amount}");
    
    //双倍加血量
    return amount * 2;
}
```
| Parameter type | Parameter name | Explanation                                           |
|----------------|----------------|-------------------------------------------------------|
| int            | amount         | 原加血量                                               |

| Return type | Explanation                                             |
|-------------|---------------------------------------------------------|
| int         | 新的加血量                                               |

### TODO
- Explain Get/Set Player Int/Float/Bool/String/Vector3 Hooks
