# 日志

## 说明

日志是将要显示的任意文本或值保存到文本文件中。 这使您可以确认代码执行的顺序是否符合您的预期，并允许您显示特别状态下的变量值。  Modding Api 内置了日志支持，因此您可以轻松创建和查看现有日志。
将内容写入日志的最简单方法是在代码中添加以下代码片段：
``` cs
	Modding.Logger.Log("Hello World");
```
这会将“Hello World”写入您的 modlog.txt [日志文件位置](#日志文件位置)。
> 注意：在您的模组中创建日志时，有一些推荐的最佳实践可以遵循，请参阅[创建日志](#创造日志)，了解有关如何创建在开发期间和发布后最有用的日志的更多详细信息 你的模组。
### 开发期间的日志
 - 通过记录可疑代码并查看显示的日志来寻找问题
 - 查看传递给您的代码或由您的代码生成的值
 - 查看游戏中的数据结构 

### 故障排除
 - 向您的mod使用者索取模组日志，以便在出现问题时进行调试。
 - 出于这个原因，如果您希望某些信息对解决问题很有价值，即使您知道预期值，您也应该记录它。

## 查看日志

 - 你可以在[日志文件](#日志文件位置)中查看
 - 您还可以启用游戏内控制台，以便在游戏中实时查看日志。
启用游戏内控制台 :

 - 关闭你的游戏
 - 转跳到存档文件夹 
 - 打开 `ModdingApi.GlobalSettings.json`文件 
 - 将 `ShowDebugLogInGame` 的值改为 true.
``` json
"ShowDebugLogInGame": true
```
 - 在游戏中您可以通过点击F10来显示和隐藏日志

> 提示：之前游戏的 modlog 保存在存档文件夹内的 `Old ModLogs` 文件夹中。

## 创造日志:

要在您的 mod 的主类中写入日志，该类继承自 [Mod 基类](mod-baseclass.md)，您可以简单地调用日志函数，例如：
```cs 
public class MyFirstMod : Mod
{
	public override void Initialize()
	{
		Log("Initialize!.");
	}
}
```
这将在日志中写入以下内容：
``` 
[MyFirstMod] - Initialize!.
```

要从您创建的任何其他类写入日志，您应该通过访问您的 mod 类的实例来使用日志功能，这是为了确保您的日志拥有Mod名称的前缀，以便您搜索它 更容易并将其与其他模组的日志区分开。

示例:
```cs 
public class MyFirstMod : Mod
{
	//创建一个包含 mod 类实例的静态变量
	public static MyFirstMod Instance;
	
	public override void Initialize()
	{
		//使用关键字“this”获取当前Mod的实例
		Instance = this;
		//在其他类可以中使用“Instance”来获取当前的 Mod 实例。
	}
}

//在不同的类中，您可以像这样访问它
public class OtherClass
{
	public void Start()
	{
		MyFirstMod.Instance.Log("Start called from OtherClass.");
	}
}
```
输出:
``` 
[MyFirstMod] - Start called from OtherClass.
```

> 注意：您可以直接使用 Modding API 中的 `Modding.Logger` 类，但不建议在发布版本中使用，因为它不会在日志中显示您的 mod 名称，让任何人都难以查看 记录以识别和解决问题。

## Player.log:  
一些unity错误和崩溃不会记录到 `modlog.txt` 而是记录到 `player.log`。 这可以在与“modlog.txt”相同的文件夹中找到。 它非常有用，特别是因为它提供了错误的堆栈跟踪，并且一些未显示在 modlog 中的错误可以显示在这里。
您可以使用unity日志api将日志写入到此文件，例如“UnityEngine.Debug.Log();”

## 日志等级:  

有 5 种不同级别的日志：  

| 枚举值(Level) | 方法名      | 描述                                                                                             |
|-------|---------------|---------------------------------------------------------------------------------------------------------|
| Error | `LogError();` | 用于记录发生的错误。                                         |
| Warn  | `LogWarn();`  | 用于记录警告。                                                                 |  
| Info  | `Log();`      | 用于普通日志。                                                                        | 
| Debug | `LogDebug();` | 用于调试代码。 使用默认设置，不会在普通用户的 modlog 上看到 |  
| Fine  | `LogFine();`  | 当您要将垃圾日志发送到 modlog 时使用。                  |  

默认情况下，在 modlog 和游戏控制台中不会看到 `LogDebug` 和 `LogFine`，要更改您可以看到的日志级别，请在存档文件夹中找到 `ModdingApi.GlobalSettings.json`。 在那里您将能够看到一个名为“LoggingLevel”的设置。 默认为 2，但建议设置为 1，这样您可以在开发时使用 `LogDebug`，并与 release 共享相同的 dll 文件（不会因为设置而向玩家的 modlog 发送垃圾日志）：
```  json
"LoggingLevel": 1
```

此范围的可接受值是 0-5，其中 5 是最少的日志记录量，0 是最多的：
| 值 | 显示的日志等级                                |
|-------|--------------------------------------------------|
| 5     | 无                                             |  
| 4     | Error.                                           |  
| 3     | Error, Warn.                                     |  
| 2     | Error, Warn, Info. (默认设置) |   
| 1     | Error, Warn, Info, Debug.                        |   
| 0     | Error, Warn, Info, Debug, Fine.                  |   
   

## 日志文件位置:  
`ModLog.txt` can be found in the same folder as your save files:
- Windows: `%APPDATA%\..\LocalLow\Team Cherry\Hollow Knight\`
- MacOS  : `~/Library/Application Support/unity.Team Cherry.Hollow Knight/`
- Linux  : `~/.config/unity3d/Team Cherry/Hollow Knight/`

在 Windows 上轻松获取 ModLog：
- 按 Windows 徽标键 + R
- 将其复制并粘贴到 `%appdata%\..\LocalLow\Team Cherry\Hollow Knight` 中，然后点击“OK”或按下回车
- ModLog.txt就在这个文件夹里面

此外，您可以创建一个[文件夹快捷方式](https://www.howtogeek.com/436615/how-to-create-desktop-shortcuts-on-windows-10-the-easy-way/) 来找到它。
