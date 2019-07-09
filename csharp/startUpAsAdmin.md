# 自启管理器（StartUpAsAdmin）

## 添加菜单栏
````xml
 <Menu Margin="0,0,0.4,295.8" Background="{x:Null}">
            <MenuItem Header="设备管理" >
                <MenuItem Header="新增设备" />
                <MenuItem Header="管理设备" />
                <MenuItem Header="删除设备" />
                <MenuItem Header="设备监控" />
            </MenuItem>
````
## 自定义圆角按钮
````xml
<Button Content="按钮名" HorizontalAlignment="Left" Height="28" Margin="284,270,0,0" VerticalAlignment="Top" Width="50" RenderTransformOrigin="2.823,0.798" BorderBrush="#FFDDDDDD" Foreground="#FF6E6B6B" FontSize="14" Background="#7FDDDDDD">
            <Button.Template>
                <ControlTemplate TargetType="{x:Type Button}">
                    <Border BorderBrush="{TemplateBinding Control.BorderBrush}" BorderThickness="0" CornerRadius="5,5,5,5" Name="PART_Background">
                        <Border.Background>
                            <SolidColorBrush x:Name="BorderBrush" Color="#CCDDDDDD"/>
                        </Border.Background>
                        <VisualStateManager.VisualStateGroups>
                            <VisualStateGroup Name="CommonStates">
                                <VisualState Name="Normal" />
                                <VisualState Name="MouseOver">
                                    <Storyboard>
                                        <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="#ddd" Duration="0:0:0.1" />
                                    </Storyboard>
                                </VisualState>
                                <VisualState Name="Pressed">
                                    <Storyboard>
                                        <ColorAnimation Storyboard.TargetName="BorderBrush" Storyboard.TargetProperty="Color" To="Transparent"/>
                                    </Storyboard>
                                </VisualState>
                            </VisualStateGroup>
                        </VisualStateManager.VisualStateGroups>
                        <ContentPresenter Content="{TemplateBinding ContentControl.Content}" HorizontalAlignment="Center" VerticalAlignment="Center" />
                    </Border>
                </ControlTemplate>
            </Button.Template>
        </Button>
````
3.弹出选择文件和文件夹对话框
````csharp
步骤1 引用组件System.Windows.Forms

步骤2 弹出选择文件对话框

	OpenFileDialog dialog = new OpenFileDialog();
	dialog.Multiselect = true;//该值确定是否可以选择多个文件
	dialog.Title = "请选择文件";
	dialog.Filter = "所有文件(*.*)|*.*";
	if (dialog.ShowDialog() == System.Windows.Forms.DialogResult.OK)
	{
   		 string file = dialog.FileName;
	}
    
       弹出选择文件夹对话框
        FolderBrowserDialog m_Dialog = new FolderBrowserDialog();  
    DialogResult result = m_Dialog.ShowDialog();  
  
    if (result == System.Windows.Forms.DialogResult.Cancel)  
    {  
        return;  
    }  
    string m_Dir = m_Dialog.SelectedPath.Trim();  
    this.textblock_filename.Text = m_Dir; 
````
4.启动后不显示任务栏图标
````
ShowInTaskbar = false;

使用示例:
	 /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            ShowInTaskbar = false;	//启动后不显示任务栏图标
            InitializeComponent();
        }
````

## WPF托盘图标类
````csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Windows;
using System.Windows.Input;
using System.Windows.Markup;
using System.Windows.Media;
using System.Windows.Threading;
using Drawing = System.Drawing;
using Forms = System.Windows.Forms;

namespace TrayIcon
{
    /// <summary>
    /// Represents a thin wrapper for <see cref="Forms.NotifyIcon"/>
    /// </summary>
    [ContentProperty("Text")]
    [DefaultEvent("MouseDoubleClick")]
    public class NotificationAreaIcon : FrameworkElement
    {
        Forms.NotifyIcon notifyIcon;

        public static readonly RoutedEvent MouseClickEvent = EventManager.RegisterRoutedEvent(
            "MouseClick", RoutingStrategy.Bubble, typeof(MouseButtonEventHandler), typeof(NotificationAreaIcon));

        public static readonly RoutedEvent MouseDoubleClickEvent = EventManager.RegisterRoutedEvent(
            "MouseDoubleClick", RoutingStrategy.Bubble, typeof(MouseButtonEventHandler), typeof(NotificationAreaIcon));

        public static readonly DependencyProperty IconProperty =
            DependencyProperty.Register("Icon", typeof(ImageSource), typeof(NotificationAreaIcon));

        public static readonly DependencyProperty TextProperty =
            DependencyProperty.Register("Text", typeof(string), typeof(NotificationAreaIcon));

        public static readonly DependencyProperty FormsContextMenuProperty =
            DependencyProperty.Register("MenuItems", typeof(List<Forms.MenuItem>), typeof(NotificationAreaIcon), new PropertyMetadata(new List<Forms.MenuItem>()));

        protected override void OnInitialized(EventArgs e)
        {
            base.OnInitialized(e);

            // Create and initialize the window forms notify icon based
            notifyIcon = new Forms.NotifyIcon();
            notifyIcon.Text = Text;
            if (!DesignerProperties.GetIsInDesignMode(this))
            {
                notifyIcon.Icon = FromImageSource(Icon);
            }
            notifyIcon.Visible = FromVisibility(Visibility);

            if (this.MenuItems != null && this.MenuItems.Count > 0)
            {
                notifyIcon.ContextMenu = new System.Windows.Forms.ContextMenu(this.MenuItems.ToArray());
            }

            notifyIcon.MouseDown += OnMouseDown;
            notifyIcon.MouseUp += OnMouseUp;
            notifyIcon.MouseClick += OnMouseClick;
            notifyIcon.MouseDoubleClick += OnMouseDoubleClick;

            Dispatcher.ShutdownStarted += OnDispatcherShutdownStarted;
        }

        private void OnDispatcherShutdownStarted(object sender, EventArgs e)
        {
            notifyIcon.Dispose();
        }

        private void OnMouseDown(object sender, Forms.MouseEventArgs e)
        {
            OnRaiseEvent(MouseDownEvent, new MouseButtonEventArgs(
                InputManager.Current.PrimaryMouseDevice, 0, ToMouseButton(e.Button)));
        }

        private void OnMouseUp(object sender, Forms.MouseEventArgs e)
        {
            OnRaiseEvent(MouseUpEvent, new MouseButtonEventArgs(
                InputManager.Current.PrimaryMouseDevice, 0, ToMouseButton(e.Button)));
        }

        private void OnMouseDoubleClick(object sender, Forms.MouseEventArgs e)
        {
            OnRaiseEvent(MouseDoubleClickEvent, new MouseButtonEventArgs(
                InputManager.Current.PrimaryMouseDevice, 0, ToMouseButton(e.Button)));
        }

        private void OnMouseClick(object sender, Forms.MouseEventArgs e)
        {
            OnRaiseEvent(MouseClickEvent, new MouseButtonEventArgs(
                InputManager.Current.PrimaryMouseDevice, 0, ToMouseButton(e.Button)));
        }

        private void OnRaiseEvent(RoutedEvent handler, MouseButtonEventArgs e)
        {
            e.RoutedEvent = handler;
            RaiseEvent(e);
        }

        public List<Forms.MenuItem> MenuItems
        {
            get { return (List<Forms.MenuItem>)GetValue(FormsContextMenuProperty); }
            set { SetValue(FormsContextMenuProperty, value); }
        }

        public ImageSource Icon
        {
            get { return (ImageSource)GetValue(IconProperty); }
            set { SetValue(IconProperty, value); }
        }

        public string Text
        {
            get { return (string)GetValue(TextProperty); }
            set { SetValue(TextProperty, value); }
        }

        public event MouseButtonEventHandler MouseClick
        {
            add { AddHandler(MouseClickEvent, value); }
            remove { RemoveHandler(MouseClickEvent, value); }
        }

        public event MouseButtonEventHandler MouseDoubleClick
        {
            add { AddHandler(MouseDoubleClickEvent, value); }
            remove { RemoveHandler(MouseDoubleClickEvent, value); }
        }

        #region Conversion members

        private static Drawing.Icon FromImageSource(ImageSource icon)
        {
            if (icon == null)
            {
                return null;
            }
            Uri iconUri = new Uri(icon.ToString());
            return new Drawing.Icon(Application.GetResourceStream(iconUri).Stream);
        }

        private static bool FromVisibility(Visibility visibility)
        {
            return visibility == Visibility.Visible;
        }

        private MouseButton ToMouseButton(Forms.MouseButtons button)
        {
            switch (button)
            {
                case Forms.MouseButtons.Left:
                    return MouseButton.Left;
                case Forms.MouseButtons.Right:
                    return MouseButton.Right;
                case Forms.MouseButtons.Middle:
                    return MouseButton.Middle;
                case Forms.MouseButtons.XButton1:
                    return MouseButton.XButton1;
                case Forms.MouseButtons.XButton2:
                    return MouseButton.XButton2;
            }
            throw new InvalidOperationException();
        }

        #endregion Conversion members
    }
}

使用方法:

	步骤1:  新建一个类,保存以上内容.
    
    步骤2: xaml配置
    
    ①添加对命名空间的引用
        xmlns:l="clr-namespace:TrayIcon"
        xmlns:form="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms"
        
        使用示例:
        <Window x:Class="StartUpAsAdmin.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:l="clr-namespace:TrayIcon"
        xmlns:form="clr-namespace:System.Windows.Forms;assembly=System.Windows.Forms"
        mc:Ignorable="d"
        
    ②在Grid中绘出托盘图标
     <l:NotificationAreaIcon x:Name="trayIcon" Text="自启管理器" Icon="images/7.ico" 			MouseClick="NotificationAreaIcon_Click" >
            <l:NotificationAreaIcon.MenuItems >
                <form:MenuItem Text="打开主页面" Click="MenuItem_Click_3"/> 
                <form:MenuItem Text="退出" Click="MenuItem_Click_4"/>
            </l:NotificationAreaIcon.MenuItems>
        </l:NotificationAreaIcon>
        
        使用示例:
        
    <Grid>
        <Grid.Background>
            <ImageBrush ImageSource="images/bg2.jpg"/>
        </Grid.Background>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        
        <l:NotificationAreaIcon Text="自启管理器" Icon="images/7.ico">
            <l:NotificationAreaIcon.MenuItems >
                <form:MenuItem Text="打开主页面" />
                <form:MenuItem Text="退出" />
            </l:NotificationAreaIcon.MenuItems>
        </l:NotificationAreaIcon>
        
        <ListBox HorizontalAlignment="Left" Height="247" VerticalAlignment="Top" Width="479" Margin="17,57,0,0" Background="#7FFFFFFF" BorderBrush="#FFDFD7D7"/>
        <Menu Margin="0,0,0.4,295.8" Background="{DynamicResource {x:Static SystemColors.ControlBrushKey}}">
            <Menu.Foreground>
                <SolidColorBrush Color="{DynamicResource 2级灰}"/>
            </Menu.Foreground>
            <MenuItem Header="首选项">
                <MenuItem Header="选择启动文件夹" Click="MenuItem_Click"/>
            </MenuItem>
            <MenuItem Header="帮助" />
            <MenuItem Header="关于" Click="MenuItem_Click_2" />
        </Menu>
        
 PS. 后台代码
 
   		/// <summary>
        /// 托盘小图标的双击事件--最小化的状态下双击还原
        /// </summary>
        /// <param name="sender"></param>
        /// <param name="e"></param>
        private void NotificationAreaIcon_Click(object sender, MouseButtonEventArgs e)
        {
			if (IsVisible == true)
            {
                Hide();
            }
            else
            {
                Show();
            }

        }
````
## 禁用最大化按钮
````csharp
ResizeMode="CanMinimize"
````
## 窗口在最前面
````csharp
Topmost = true;
````
## 读取和写入文本
````csharp
读取:
string = File.ReadAllText(@"config.info");

写入:
FileStream fs = new FileStream("config.info", FileMode.Create, FileAccess.Write);   //实例化FileStream对象fs
            StreamWriter sw = new StreamWriter(fs); //实例化StreamWriter对象sw
            fs.SetLength(0);    //清空文件
            sw.Write(m_Dir);   //写入字符串
            sw.Close();
````
## 用记事本打开文本文件
````csharp
System.Diagnostics.Process.Start("notepad.exe", "readme.txt");
````
## 枚举一个文件夹下所有(特定类型)文件
````csharp
DirectoryInfo TheFolder = new DirectoryInfo(m_Dir);
filelist.ItemsSource=TheFolder.GetFiles("*.exe");
````
## 需要管理员权限的程序开机自启思路
````csharp
利用stsrt.vbs脚本来实现开机自启

Set shell =Wscript.createobject("wscript.shell")  
path =createobject("Scripting.FileSystemObject").GetFile(Wscript.ScriptFullName).ParentFolder.Path  
a = shell.run(path& "\StartUpAsAdmin.exe",0,false)  

Ps.脚本文件与主程序放置在同一个文件夹下

或者用ahk程序实现.(后来我用ahk创建了一个daemon.exe)
````
## 自启注册表位置
````csharp
string startupPath = System.Windows.Forms.Application.ExecutablePath;
            RegistryKey hklm = Registry.CurrentUser;
            RegistryKey lgn = hklm.OpenSubKey(@"Software\Microsoft\Windows\CurrentVersion\Run", true);
            //System.Windows.MessageBox.Show(startupPath);
            lgn.SetValue("test", startupPath, RegistryValueKind.String);
````
## 文件特殊路径
````csharp
//   应用程序的路径，不带文件名   
  Application.StartupPath();   
  //   产品名称   
  Application.ProductName;   
  //   产品版本（可由.net自动升成版本控制）   
  Application.ProductVersion
  // 程序路径
   string startupPath = System.Windows.Forms.Application.ExecutablePath;
````
## 判断程序是由开机自启还是用户启动
````csharp
本次项目实战采用了,采用文件读写的方式实现目的:

主要思路如下,可供以后借鉴:  ①创建一个守护程序 daemon.exe
						  ②将守护程序加入启动项注册表中
                          ③开机由守护程序启动主程序
                          关键: 守护程序在启动主程序之前,向磁盘文件config.info写入1(表示为由守护程序启动)
                          ④主程序在初始化之前,先判断config中的信息(若为1则执行相应操作),否则不执行.
                          关键:在主程序初始化完毕后,不论config.info中的内容是什么,删除该文件(复位,以便于下次启动判断)
                          
后来发现注册表就是专门用来解决这个问题的,以后项目中可以试试注册表.
````
## 设置守护程序开机启动
```csharp
try
{
    if (isAutoStart == "1")
    {
        string startupPath = System.Windows.Forms.Application.StartupPath;
        startupPath += @"\daemon.exe";
        RegistryKey hklm = Registry.CurrentUser;
        RegistryKey lgn = hklm.OpenSubKey(@"Software\Microsoft\Windows\CurrentVersion\Run", true);
        lgn.SetValue("daemon", startupPath, RegistryValueKind.String);
    }
}
catch
{
    //do nothing
}
```
## 隐藏窗体以及任务栏窗口
````csharp
Hide();
ShowInTaskbar = false;
````