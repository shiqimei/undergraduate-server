# 安速（AnSpeed）

## 限制输入框的输入的字符类型
````csharp
// 限制文本框只能输入数字
private void textBox1_KeyPress(object sender, KeyPressEventArgs e)
{
    if(!Char.IsNumber(e.KeyChar) && e.KeyChar != (char)8) // (char)8 表示退格键
    {
        e.Handled = true;
    }
}
````
## 注册表相关操作
````csharp
(1) 初始化子键AnSpeed
RegistryKey hklm = Registry.CurrentUser;
RegistryKey lgn0 = hklm.OpenSubKey(@"Software\AnSpeed", true);
if (lgn0 == null)   // 若注册表中还未有软件的注册表，则初始化注册表
{
    RegistryKey lgn = hklm.OpenSubKey(@"Software", true).CreateSubKey("AnSpeed");
    lgn.SetValue("stuid", "", RegistryValueKind.String);    // 学号
    lgn.SetValue("stupasswd", "", RegistryValueKind.String);   // 密码
    lgn.SetValue("stutoken", "", RegistryValueKind.String);   // 令牌
}
    
(2) 更新注册表某一项的值
// 将设置的颜色保存到注册表中
RegistryKey hklm = Registry.CurrentUser;
RegistryKey lgn = hklm.OpenSubKey(@"Software", true).CreateSubKey("BatteryIcon");
lgn.SetValue("chargingColor", new ColorConverter().ConvertToString(TrayIcon.chargingColor), RegistryValueKind.String);
 
(3) 从注册表中取值
RegistryKey hklm = Registry.CurrentUser;
RegistryKey lgn = hklm.OpenSubKey(@"Software\AnSpeed", true);
lgn.SetValue("rempasswd", "true", RegistryValueKind.String);
````
## 设置文件属性为隐藏
````csharp
1.设置文件属性为隐藏
````csharp
FileInfo fileInfo = new FileInfo("C:\\Windows\\Temp\\" + filename);    // 隐藏该文件
fileInfo.Attributes |= FileAttributes.Hidden;
````
## 图片按钮的hover事件
````csharp
// 密码问号按钮
private void Image_MouseMove(object sender, System.Windows.Input.MouseEventArgs e)
{
    qusetion_passwd.Source = new BitmapImage(new Uri("Images/question_hover.png", UriKind.Relative));
}

private void qusetion_passwd_MouseLeave(object sender, System.Windows.Input.MouseEventArgs e)
{
    qusetion_passwd.Source = new BitmapImage(new Uri("Images/question.png", UriKind.Relative));
}

private void qusetion_passwd_MouseDown(object sender, System.Windows.Input.MouseButtonEventArgs e)
{
    Process.Start("http://www.anspeed.cn/qa/passwd/");
}
````
## 带参数启动
````csharp
        // 添加启动参数的处理
        protected override void OnStartup(StartupEventArgs e)

        {
            if (e.Args.Length > 0)
                MessageBox.Show(e.Args.ToString());
        }
````
## 读取文本信息
````csharp
File.ReadAllText() // 将文本内容以字符串的形式返回
File.ReadAllLines() // 将文本内容以数组形式保存
````
## 获取当前用户路径
````csharp
@"C:\Users\" + Environment.UserName + @"\OpenVPN\log\1.log")
````
## 用StreamReader读取文本(解决文本被占用的问题)
````csharp
FileStream fs = new FileStream(@"C:\Users\" + Environment.UserName + @"\OpenVPN\log\1.log", FileMode.Open, FileAccess.Read, FileShare.ReadWrite);
StreamReader sr = new StreamReader(fs, Encoding.Default);
string line;
while ((line = sr.ReadLine()) != null)
{
    MessageBox.Show(line);
}
````
## 强制结束某个进程
````csharp
Bat.RunCmd("taskkill /F /IM openvpn-gui.exe /T"); // /F表示强制 /IM表示通过名字 /T表示关闭进程树
````
## 产生一个大于等于最小值，**小于**最大值的随机数
````csharp
new Random().Next(1, 3) // 产生一个1或者2的随机数
````
## 重启自己提升权限
````csharp
Process p = new Process();
p.StartInfo.FileName = Process.GetCurrentProcess().MainModule.FileName;
p.StartInfo.Arguments = "Speed";
p.StartInfo.UseShellExecute = true;
p.StartInfo.Verb = "runas";
p.Start();
Environment.Exit(0);
````
## 托盘图标刷新类
[下载链接](https://download.csdn.net/download/weixin_42038849/10538360)
## 屏蔽输入法
````csharp
InputMethod.IsInputMethodEnabled="False"		<!-- xaml属性 -->
````
## 与服务器交互HTTP
````csharp
public static void TokenVerify(string token)
        {
            HttpWebRequest req = (HttpWebRequest)WebRequest.Create("https://app.lolimay.cn/cs.php?id=" + token);
            req.Method = "GET";
            using (WebResponse wr = req.GetResponse())
            {
                MessageBox.Show(new StreamReader(wr.GetResponseStream(), 					Encoding.GetEncoding("gb2312")).ReadToEnd());
            }
        }
````
注意: 与HTTP的交互必须通过委托主线程解决
````
Dispatcher.Invoke(new Action(delegate
{
    NetWork.TokenVerifySuccess(stutoken.Substring(0, 17) + input_stuid.Text); //注册该学号为已体验 同时该令牌失效
}));
````
## WPF进度条动画
````csharp
    for (int i = 0; i <= 1000; i++)
    {
        Thread.Sleep(10);
        Dispatcher.Invoke(new Action(delegate 
        {
            bar_progress.Value += 0.1;
            bar.MinWidth += 0.435; 
            label_process.Margin = new Thickness(i*0.435, 18, -10, 0); // 移动标签
            label_process.Content = Convert.ToInt32(i/10) + "%";
        }));
    }
````
## 注册表(Binary和Dword)在C#中的写法
````csharp
RegistryKey hklm = Registry.CurrentUser;
RegistryKey lgn = hklm.OpenSubKey(@"Software\OpenVPN-GUI", true);
if (lgn == null) lgn = hklm.OpenSubKey(@"Software", true).CreateSubKey("OpenVPN-GUI");
lgn.SetValue("show_balloon", "00000000", RegistryValueKind.DWord);    // ballon
lgn.SetValue("silent_connection", "00000001", RegistryValueKind.DWord);   // 静默登录
lgn.SetValue("version", new byte[] {11, 00, 10, 00, 00, 00, 00, 00}, RegistryValueKind.Binary);
````
## 字符串加密解密类
````csharp
    class Security
    {
       //默认密钥向量
        private static byte[] Keys = { 0x12, 0x34, 0x56, 0x78, 0x90, 0xAB, 0xCD, 0xEF };

        /// <summary>
        /// DES加密字符串
        /// </summary>
        /// <param name="encryptString">待加密的字符串</param>
        /// <param name="encryptKey">加密密钥,要求为8位</param>
        /// <returns>加密成功返回加密后的字符串，失败返回源串 </returns>
        public static string EncryptDES(string encryptString, string encryptKey)
        {
            try
            {
                byte[] rgbKey = Encoding.UTF8.GetBytes(encryptKey.Substring(0, 8));//转换为字节
                byte[] rgbIV = Keys;
                byte[] inputByteArray = Encoding.UTF8.GetBytes(encryptString);
                DESCryptoServiceProvider dCSP = new DESCryptoServiceProvider();//实例化数据加密标准
                MemoryStream mStream = new MemoryStream();//实例化内存流
                                                          //将数据流链接到加密转换的流
                CryptoStream cStream = new CryptoStream(mStream, dCSP.CreateEncryptor(rgbKey, rgbIV), CryptoStreamMode.Write);
                cStream.Write(inputByteArray, 0, inputByteArray.Length);
                cStream.FlushFinalBlock();
                return Convert.ToBase64String(mStream.ToArray());
            }
            catch
            {
                return encryptString;
            }
        }

        /// <summary>
        /// DES解密字符串
        /// </summary>
        /// <param name="decryptString">待解密的字符串</param>
        /// <param name="decryptKey">解密密钥,要求为8位,和加密密钥相同</param>
        /// <returns>解密成功返回解密后的字符串，失败返源串</returns>
        public static string DecryptDES(string decryptString, string decryptKey)
        {
            try
            {
                byte[] rgbKey = Encoding.UTF8.GetBytes(decryptKey);
                byte[] rgbIV = Keys;
                byte[] inputByteArray = Convert.FromBase64String(decryptString);
                DESCryptoServiceProvider DCSP = new DESCryptoServiceProvider();
                MemoryStream mStream = new MemoryStream();
                CryptoStream cStream = new CryptoStream(mStream, DCSP.CreateDecryptor(rgbKey, rgbIV), CryptoStreamMode.Write);
                cStream.Write(inputByteArray, 0, inputByteArray.Length);
                cStream.FlushFinalBlock();
                return Encoding.UTF8.GetString(mStream.ToArray());
            }
            catch
            {
                return decryptString;
            }
        }
    }
````


## 遍历list
````csharp
string s="";
foreach (var ele in NetWork.GetAllAdslName())
{
    s += ele.ToString() + "\n";
}
MessageBox.Show(s);
````

## 删除注册表子项
````csharp
RegistryKey hklm = Registry.CurrentUser;
hklm.OpenSubKey(@"Software", true).DeleteSubKey("AnSpeed");
````
## 删除桌面快捷方式
````csharp
File.Delete(Environment.GetFolderPath(Environment.SpecialFolder.Desktop) + "\\安速.lnk" );
````

## 新建宽带连接
````csharp
1. Nuget安装Dotras.dll
2. 新建宽带连接函数
public void CreateConnect(string ConnectName)
{
    RasDialer dialer = new RasDialer();
    RasPhoneBook book = new RasPhoneBook();
    try
    {
        book.Open(RasPhoneBook.GetPhoneBookPath(RasPhoneBookType.User));
        if (book.Entries.Contains(ConnectName))
        {
            book.Entries[ConnectName].PhoneNumber = " ";
            book.Entries[ConnectName].Update();
        }
        else
        {
            System.Collections.ObjectModel.ReadOnlyCollection<RasDevice> readOnlyCollection = RasDevice.GetDevices();
            RasDevice device = RasDevice.GetDevices().Where(o => o.DeviceType == RasDeviceType.PPPoE).First();
            RasEntry entry = RasEntry.CreateBroadbandEntry(ConnectName, device);
            entry.PhoneNumber = " ";
            book.Entries.Add(entry);
        }
    }
    catch (Exception)
    {
        // do nothing
    }
}
````
## 删除宽带连接
````csharp
1. Nuget安装Dotras.dll
2. 函数
public void RasRemove(string ConnectName)
{
    RasDialer dialer = new RasDialer();
    RasPhoneBook book = new RasPhoneBook();
    try
    {
        book.Open(RasPhoneBook.GetPhoneBookPath(RasPhoneBookType.User));
        if (book.Entries.Contains(ConnectName))
        {
            book.Entries[ConnectName].Remove();
        }
    }
    catch (Exception)
    {
        // do nothing
    }
}
````
## 获取当前程序所在的目录
````csharp
Path.GetDirectoryName(Process.GetCurrentProcess().MainModule.FileName);
````
## 获取系统特殊文件夹(通法)
````csharp
Environment.GetFolderPath(Environment.SpecialFolder.StartMenu)
````
## 提取字符串中所有的数字
````csharp
System.Text.RegularExpressions.Regex.Replace(label_version.Content.ToString(), @"[^0-9]+", "")
````
## 删除字符串中的第一个字符
````csharp
Substring(1)
````
## 下载文件
````csharp
public void downLoad(string url, string localAdress)
{
    WebRequest request = WebRequest.Create(url); //下载的路径
    WebResponse respone = request.GetResponse();
    double downLoadFileSize = (double)respone.ContentLength/1048576; //获取下载文件的总大小
    ThreadPool.QueueUserWorkItem((obj) =>
    {
        Stream netStream = respone.GetResponseStream();
        Stream fileStream = new FileStream(localAdress, FileMode.Create);
        byte[] read = new byte[1024];
        long progressBarValue = 0; //用于存已下载多少
        double currentSize = 0; //将字节转化为MB
        int realReadLen = netStream.Read(read, 0, read.Length);
        while (realReadLen > 0)
        {
            fileStream.Write(read, 0, realReadLen);
            progressBarValue += realReadLen;
            currentSize = (double)progressBarValue / 1048576;
            Dispatcher.Invoke(new Action(delegate { top_warning.Content = "正在下载最新版本: " + currentSize.ToString("0.00") + "MB (" + downLoadFileSize.ToString("0.00") + "MB)"; })); //显示
            realReadLen = netStream.Read(read, 0, read.Length);
        }
        netStream.Close();
        fileStream.Close();
    }, null);
}
````

## 遍历所有宽带连接
````csharp
[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
public struct RasEntryName      //define the struct to receive the entry name
{
    public int dwSize;
    [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 256 + 1)]
    public string szEntryName;
#if WINVER5
        public int dwFlags;
        [MarshalAs(UnmanagedType.ByValTStr,SizeConst=260+1)]
        public string szPhonebookPath;
#endif
}

[DllImport("rasapi32.dll", CharSet = CharSet.Auto)]

public extern static uint RasEnumEntries(
    string reserved,              // reserved, must be NULL
    string lpszPhonebook,         // pointer to full path and file name of phone-book file
    [In, Out]RasEntryName[] lprasentryname, // buffer to receive phone-book entries
    ref int lpcb,                  // size in bytes of buffer
    out int lpcEntries             // number of entries written to buffer
);

public static List<string> GetAllAdslName()
{
    List<string> list = new List<string>();
    int lpNames = 1;
    int entryNameSize = 0;
    int lpSize = 0;
    RasEntryName[] names = null;
    entryNameSize = Marshal.SizeOf(typeof(RasEntryName));
    lpSize = lpNames * entryNameSize;
    names = new RasEntryName[lpNames];
    names[0].dwSize = entryNameSize;
    uint retval = RasEnumEntries(null, null, names, ref lpSize, out lpNames);

    //if we have more than one connection, we need to do it again
    if (lpNames > 1)
    {
        names = new RasEntryName[lpNames];
        for (int i = 0; i < names.Length; i++)
        {
            names[i].dwSize = entryNameSize;
        }
        retval = RasEnumEntries(null, null, names, ref lpSize, out lpNames);
    }

    if (lpNames > 0)
    {
        for (int i = 0; i < names.Length; i++)
        {
            list.Add(names[i].szEntryName);
        }
    }
    return list;
}
````