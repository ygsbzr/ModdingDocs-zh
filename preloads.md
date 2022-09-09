---
nav_order: 6
---
# Preloading game objects
## Introduction
To modify in game behaviors you typically need access to the GameObject that controls that behavior. To get access to said GameObject, you need to be in the same "scene" or level as it. This creates a problem such that, if you want to spawn a copy of an enemy, you need access to that enemy without needing the player to be in that scene before you can spawn it.

如果您需要获取一个敌人，但是不需要玩家在该场景中才能获取它，那么您应当使用预加载来获取敌人。

预加载是mod告诉 Modding API 需要加载某个游戏对象的一种方式。Modding Api 在游戏首次加载时尝试加载游戏对象，您可以使用该游戏对象。
## 如何预加载一个对象

要预加载游戏对象，您的 mod 类必须重写 `GetPreloadNames`方法，如下所示：
```cs
public override List<(string, string)> GetPreloadNames()
{
    return new List<(string, string)>
    {
        ("GG_Hornet_2", "Boss Holder/Hornet Boss 2"),
        ("Cliffs_01","Cornifer Card"),
        ("sharedassets32","Shot Mawlek")
    };   
}
```
In the list returned by `GetPreloadNames` each element is of the form `(SceneName, GameObjectPath)`. `SceneName` is the internal name of a level and `GameObjectPath` is the path of the GameObjects going from the root of the scene to the desired object.

GameObjects can have a parent child relationship, so the GameObjectPath should be a list of all ancestors in the order of decending seniority separated by a `/`.  I.e. `GreatGrandParent/GrandParent/Parent/Child` for loading Child.

`SceneName` 和 `GameObjectPath` 使 Modding Api 可以寻找所需的游戏对象，并且该游戏对象将在初始化时提供。

The SceneName and this path allow the modding api to locate the exact object that is desired and this object will be provided during initialization. For this reason your mod must implement a `Initialize` method similar to this when expecting preloads:

```cs
public override void Initialize(Dictionary<string, Dictionary<string, GameObject>> preloadedObjects)
{
   var BossPrefab = preloadedObjects["GG_Hornet_2"]["Boss Holder/Hornet Boss 2"];
   var CardPrefab = preloadedObjects["Cliffs_01"]["Cornifer Card"];
   var Shot = preloadedObjects["sharedassets32"]["Shot Mawlek"];
   Object.DontDestroyOnLoad(BossPrefab);
   Object.DontDestroyOnLoad(CardPrefab);
}
``` 
You can then access your GameObject by doing `preloadedObjects[SceneName][GameObjectPath]`.
