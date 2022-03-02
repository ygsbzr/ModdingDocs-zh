# 预加载游戏对象
## 说明

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
        ("Cliffs_01","Cornifer Card")
    };   
}
```
在`GetPreloadNames` 的返回值中，每个元素的格式为 `(SceneName, GameObjectPath)`。 
 
`SceneName` 是场景的内部名称，`GameObjectPath` 是游戏对象的路径。

 `GameObjectPath` 应该类似于`根对象/父对象/子对象`
> 注意：您可以使用 [Scene Dump](https://prashantmohta.github.io/ModdingDocs/#todo-section) 来查找特定 GameObject 的路径。

`SceneName` 和 `GameObjectPath` 使 Modding Api 可以寻找所需的游戏对象，并且该游戏对象将在初始化时提供。

要获取预加载的游戏对象，您的 mod 必须重写`Initialize(Dictionary<string, Dictionary<string, GameObject>> preloadedObjects)`方法，并使用 `preloadedObjects[SceneName][GameObjectPath]` 来获取预加载的游戏对象。
示例:
```cs
public override void Initialize(Dictionary<string, Dictionary<string, GameObject>> preloadedObjects)
{
   var BossPrefab = preloadedObjects["GG_Hornet_2"]["Boss Holder/Hornet Boss 2"];
   var CardPrefab = preloadedObjects["Cliffs_01"]["Cornifer Card"];
   Object.DontDestroyOnLoad(BossPrefab);
   Object.DontDestroyOnLoad(CardPrefab);
}
```
> 注意：预加载对象会增加游戏的启动时间。 您应该尽可能减少预加载对象的数量。
