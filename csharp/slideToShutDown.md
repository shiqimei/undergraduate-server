# 滑屏关机（SlideToShutDown）

## 用管理员权限执行你的WPF程序
````js
步骤1  在Visual Studio 中--解决方案资源管理器--右键项目名称--属性，找到“安全性”选项
步骤2  勾选“启用ClickOnce安全设置”
步骤3  这时，在项目下面会多出一个“app.manifest”的文件
	  选中它，并找到代码段<requestedExecutionLevel level="asInvoker" uiAccess="false" />
      将其改为：<requestedExecutionLevel level="requireAdministrator" uiAccess="false" />
步骤4  将“启用ClickOnce安全设置”前面的勾去掉后再编译运行。 不然程序会报错无法运行。
```` 

## 开机自启目录
````bash
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
````

## 运行软件
````csharp
System.Diagnostics.Process.Start(path)
````

## 判断某一进程是在运行
````csharp
Process[] ps = Process.GetProcesses();
bool dr = false;
foreach (System.Diagnostics.Process p in ps)
{
    if (p.ProcessName == "DrClient" || p.ProcessName == "DrMain"
    {
        dr = true;
    }
}
````
## 杀掉某个进程
````csharp
Process killpro = Process.GetProcessesByName("进程名");
killpro.Kill();
````
## MP3 播放类
````csharp
public static uint SND_ASYNC = 0x0001; public static uint SND_FILENAME = 0x00020000;[DllImport("winmm.dll")]
public static extern uint mciSendString(string lpstrCommand, string lpstrReturnString, uint uReturnLength, uint hWndCallback);

/// <summary>
/// 播放指定mp3文件
/// </summary>
/// <param name="filename">文件路径</param>
public static void Play(string filename)
{
    mciSendString(@"close temp_alias", null, 0, 0);
    mciSendString(@"open """ +filename +@""" alias temp_alias", null, 0, 0);
    mciSendString("play temp_alias", null, 0, 0);  	// 在temp_alias后添加 repeat 可以循环播放
}
````