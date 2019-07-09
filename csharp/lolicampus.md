# 校园网自动登录（LoliCampus）

## 点击打开QQ聊天窗口
````csharp
System.Diagnostics.Process.Start("tencent://message/?uin=1404363070");
````
## 实现按钮变色
````csharp
private void Label1_MouseMove(object sender, MouseEventArgs e)
{
    install_button.ImageLocation = @".\images\1.png";
}

private void Install_button_MouseLeave(object sender, EventArgs e)
{
    install_button.ImageLocation = @".\images\2.png";
}
//需要注意的是MouseMove事件是的第二个参数类型是MouseEventArgs类型
//而MouseLeave事件的第二个参数类型是EventArgs类型
````
## 弹出子窗口，且在子窗体未操作完成之前不允许操作父窗体
````csharp
 写法1：        QuesForm quesForm = new QuesForm();
            	quesForm.ShowDialog();
 
 写法2：			new QuesForm().ShowDialog();    
 				//ShowDialog()方法要求在子窗口没有关闭前，是不能操作父窗口的
````
## 实现等待几秒再继续执行的效果(延时效果）
````csharp
步骤1 在窗体的构造器中加入： CheckForIllegalCrossThreadCalls = false;

			/**
            * 解释：
            * 在多线程程序中,新创建的线程不能访问UI线程创建的窗口控件,
			* 这个时候如果你想要访问窗口的控件,那么你可以将窗口构造函数
			* 中的CheckForIllegalCrossThreadCalls设置为false.这是线程就
			* 能安全的访问窗体控件了.
            */
            如：
            public MainForm()
       		{
         	  	 InitializeComponent();
        	   	 CheckForIllegalCrossThreadCalls = false;
    	    }
            
步骤2 将方法改成异步（async），并且用Action封装一个方法，再用await Task.Run()运行异步方法

		 如：
         async void label1_Click(object sender, EventArgs e)
        {
            message1.Show();
            
            //用Action封装一个方法，再用await Task.Run()执行异步方法
            Action action= AsyncMethod;	
            await Task.Run(action);
        }
        
步骤3 创建异步方法AsyncMethod

		如：
        public void VersionMessage()
        {
            Thread.Sleep(2000);		//延时2秒
            message1.Text = "抱歉，未检查到新版本！";
            Thread.Sleep(2000);
            version_message1.Hide();
            message1.Text = "正在检查更新...";
        }
        
        /**注：为什么不能直接在主线程Thread.Sleep(2000); 而要如此大费周章？
        *在主线程中直接用thread，整个主线程就会处于挂起状态，相当于程序卡了2秒...
        *这当然不是我们愿意看到的~
        *所以，需要新开辟线程去执行我们的延时代码~
        */

补充: WPF程序不允许非UI线程更新UI内容,这可以通过委托曲线救国:
		 并且 WPF 不需要 步骤1
	例:
	控件名.Dispatcher.Invoke(new System.Action(delegate { 控件名.IsEnabled = false; })); 
````
## C#中的退出（退出窗体和程序）
````csharp
1.	this.Close();   只是关闭当前窗口，若不是主窗体的话，是无法退出程序的，另外若有托管线程（非主线程），也无法干净地退出；
2.	Application.Exit();  强制所有消息中止，退出所有的窗体，但是若有托管线程（非主线程），也无法干净地退出；
3.	Application.ExitThread(); 强制中止调用线程上的所有消息，同样面临其它线程无法正确退出的问题；
4.	System.Environment.Exit(0);   这是最彻底的退出方式，不管什么线程都被强制退出，把程序结束的很干净。

值得注意的是：	this.Close();  仅仅是关闭当前窗体，如果窗体中有循环，循环仍会继续运行。
````
## 判断一个宽带连接是否存在
````csharp
步骤1 新建一个Adsl.cs类

using System.Collections.Generic;
using System.Runtime.InteropServices;

namespace Campus
{
    class Adsl
    {
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
    }
}

步骤2 使用方法

 				List<string> mList = new List<string>();
                mList = Adsl.GetAllAdslName();
                string campus = "campus";	//判断是否存在“Campus”宽带连接
                bool ifExist = false;				//默认不存在
                foreach (string element in mList)
                {
                    if (campus == element) ifExist = true;		//如果找到了，则设为true
                }
                if (!ifExist)   //如果宽带连接不存在
                {
                    ...
                }
````
## C#运行一个脚本文件(bat或者vbe)的方法
````csharp
					Process proc = null;
                    try
                    {
                        proc = new Process();
                        proc.StartInfo.FileName = @"C:\\myVbe.vbe";		//文件名
                        proc.StartInfo.Arguments = string.Format("10");		//参数
                        proc.StartInfo.CreateNoWindow = false;
                        proc.Start();
                        proc.WaitForExit();
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Exception Occurred :{0},{1}", ex.Message, ex.StackTrace.ToString());
                    }
````
## C#对文件的操作总结
````csharp
情形1 对文件的基本操作

File.Create（@"D:\Test\Debug1\test.txt"）		//创建文件
File.Open(@"D:\Test\Debug1\test.txt",FileMode.Append)	//打开文件
File.AppendAllText(@"D:\Test\Debug1\test.txt","Hello");		//追加文件
File.Copy(@"D:\Test\Debug1\test.txt", @"D:\Test\Debug2\test1.txt", true);	//复制文件
File.Move(@"D:\Test\Debug1\test.txt", @"D:\Test\Debug3\test2.txt");		//移动文件
File.Delete(@"D:\Test\Debug1\test.txt");													//删除文件
File.SetAttributes(@"D:\Test\Debug1\test.txt", FileAttributes.Hidden);	//设置文件属性

注：FileMode.Append 打开现有文件准备向文件追加数据，只能同FileAccess.Write一起使用

情形2 对文件的读取和写入

	FileStream fs = new FileStream("C:\\autodial.bat", FileMode.Create, FileAccess.Write);   //实例化FileStream对象fs
    StreamWriter sw = new StreamWriter(fs);	//实例化StreamWriter对象sw
    fs.SetLength(0);    //清空文件
    sw.Write(result);   //写入字符串
    sw.Close();
````
## C#修改注册表文件
````csharp
    RegistryKey hklm = Registry.LocalMachine;
    RegistryKey lgn = hklm.OpenSubKey(@"SOFTWARE\Policies\Microsoft\Windows\NetworkConnectivityStatusIndicator", true);
    lgn.SetValue("EnableActiveProbing", 0x0);	//设置“EnableActiveProbing”项的值为“0x0”
````
## 调用系统警告音
````csharp
System.Media.SystemSounds.Hand.Play();  //系统警告音
````
## 按下按钮后，取消注册事件
````csharp
  button1.Click -= new EventHandler(Method);	//取消注册该事件，防止用户多次点击
  ...
  button1.Click += new EventHandler(Method); 	//重新注册事件
````
## 输入框按下Enter键事件
````csharp
如：
	    private void textBox2_KeyDown(object sender, KeyEventArgs e)
        {
            if (e.KeyCode == Keys.Enter)
            {
                pictureBox1_Click_1(sender, e);   //调用按钮的事件处理代码
            }
        }
````
## 字符串处理——Substring方法
````csharp
Substring(m, n)	用法：从第m个字符开始截取，截取n个字符

	注意：第一个字符的序号是0
````
## 屏蔽所有按键
````csharp
private void input_passwd_PreviewKeyDown(object sender, System.Windows.Input.KeyEventArgs e)
{
    if (button_login.Content.ToString() == "退出登录")
    {
        e.Handled = true;
        return;
    }
}
````
## confirm框
````csharp
if(MessageBox.Show("退出安速将不再加速并导致校园网掉线,确定继续吗?", "提示", MessageBoxButton.YesNo) == MessageBoxResult.Yes)
{
    TaskBarUtil.RefreshNotificationArea();
    Environment.Exit(0);
}
````
