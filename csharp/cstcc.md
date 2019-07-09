# 班级自习时间图表生成器（Cstcc）

## WPF实现无边框拖动
````csharp
步骤1 在MainWindow.xaml.cs中添加Window_MouseDown事件
		
        如：
        private void Window_MouseDown(object sender, MouseButtonEventArgs e)
        {
            try
            {
                DragMove();		//实现无边框窗体的拖动
            }
            catch
            {
                // do nothing
            }
        }
        
        注：用try..catch语句是因为防止抛异常

步骤2 在MainWindow.xaml中绑定事件 MouseDown="Window_MouseDown"
````
## 路由事件（RoutedEvent）
````csharp
路由事件的事件拥有者和事件的相应者之间则没有直接的显式订阅关系
事件的拥有者则只负责激发事件
事件将由谁相应它并不知道
事件的响应者则有事件的监听器，针对事件进行监听
当有此类事件传递至此事件响应者就使用事件处理器来相应事件并决定此事件是否继续传递。
````

## 设置窗体样式：None
````csharp
WindowStyle="None"
````
## 设置边框宽度
````csharp
BorderThickness="20"

注：	WPF程序中的单位是与设备无关的单位
		每个单位是1/96英寸，如果电脑的DPI设置为96(每个英寸96个像素)
		那么此时每个WPF单位对应一个像素
````
## 注册鼠标事件
````csharp
MouseDown="Window_MouseDown"	//绑定MouseDown事件

注：	如果用户在一个元素上点击，那么最少三个事件会被触发，事件发生顺序：
		1、mousedown,当用户在这个元素上按下鼠标键的时候
		2、mouseup，当用户在这个元素上松开鼠标键的时候
		3、click，当一个mousedown和一个mouseup都在这个元素上被检测到的时候发生
````
## mc标记、d标记与x标记
````csharp
有两个命名空间我们要注意一下的：
xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"

d可以理解为是设计时的状态（DesignWidth）
d:DesignWidth="480"就是说这个宽度和高度只是在设计时有效，
也就是我们在设计器中看到的大小，并不意味着真正运行起来是这个值。

mc是标记兼容性相关的内容
mc:Ignorable="d"就是告诉编译器在实际运行时，忽略设计时设置的值。

x：class类
用于配置 XAML 编译，在标记和代码隐藏之间连接分部类。
代码分部类在一个独立的代码文件中定义
标记分部类由代码生成过程在 XAML 编译期间创建。
````
## 背景透明
````csharp
Background="Transparent"
````
## 引用控件库DMSkin.WPF.Small
````csharp
步骤1 引用右键->添加引用->导入DMSkin.WPF.Small.dll

步骤2 using DMSkin.WPF.Small以及继承DMSkinWindow

	  如：
           using DMSkin.WPF.Small;

		   namespace MyFirstWpf
           {
 		  	   /// <summary>
 			   /// MainWindow.xaml 的交互逻辑
 			   /// </summary>
 		 	 public partial class MainWindow : DMSkinWindow
  		  	 {
  		     	 public MainWindow() => InitializeComponent();	//初始化
 		  	 }
		    }
 
步骤3 前端页面MainWindow.xaml中的标记改成 DMSkin:DMSkinWindow x:Class="MyFirstWpf.MainWindow"

		如：
    	<DMSkin:DMSkinWindow x:Class="MyFirstWpf.MainWindow"
	    xmlns:DMSkin="clr-namespace:DMSkin.WPF.Small;assembly=DMSkin.WPF.Small"
   	    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
	    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
 	    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
 	    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        ...
````
## xmlns的作用
````csharp
全称xml namespace, 即xml的命名空间

引入命名空间的目的是为了解决命名冲突
所谓命名空间就是一个可以由用户自己定义的作用域
在不同的作用域中可以定义相同名字的变量，互不干扰，系统能够区分它们。
````
## DMWindow常见属性
````csharp
DMWindowShadowSize="20"			四周边框阴影大小
````
## grid
````csharp
Xaml中的Grid控件相当于Html中的Table标签，用表格的行列来控制布局。
<Grid.RowDefinitions>//这个标签用来对Grid的行属性进行设置，可以设置高度
<Grid.ColumnDefinitions>//设置Grid列属性的样式

如：
<Grid x:Name="Grid1" Grid.Row="3" Grid.Column="3" Height="300" Margin="12,0,12,0" >//Row，Column属性用来设置Grid几行几列
        <Grid.RowDefinitions>//这个标签用来对Grid的行属性进行设置，可以设置高度等
            <RowDefinition Height="100"></RowDefinition>
            <RowDefinition Height="100"></RowDefinition>
            <RowDefinition Height="100"></RowDefinition>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>//设置Grid列属性的样式，
            <ColumnDefinition Width="150"></ColumnDefinition>
            <ColumnDefinition Width="150"></ColumnDefinition>
            <ColumnDefinition Width="150"></ColumnDefinition>
        </Grid.ColumnDefinitions>
//设置完毕的grid的行列属性样式之后就可以添加，控件，控件中的Grid.Row和Gird.Column用来设置该控件在Grid位于第几行，第几列
        <Button x:Name="btn1" Content="Button1" Grid.Row="0" Grid.Column="0"></Button>
        <Button x:Name="btn2" Content="Button2" Grid.Row="1" Grid.Column="0"></Button>
        <Button x:Name="btn3" Content="Button3" Grid.Row="2" Grid.Column="1"></Button>
        <Button x:Name="btn4" Content="Button4" Grid.Row="0" Grid.Column="2"></Button>
    </Grid>
````
## x:Name
````csharp
x:Name是XAML中唯一的标识
````
## border
````csharp
Border 是一个装饰的控件，此控件绘制一个边框、一个背景，在 Border 中只能有一个子件但它的子控件是可以包含多个子控件的。

Border 的几个主要的属性：
	Background:此属性设置 背景颜色
	BorderBrush:此属性设置  边框颜色
	BorderThickness:此属性设置 边框的宽度
	CornerRadius:此属性设置 每一个角圆的弧度，
	Padding:此属性设置 Border 里的内容与边框的之间的间隔，此属性是一个 Thickness 对象，可以使用此对象为每一边的间隔进行设置。
````

## 为什么C# WPF 应用程序中找不到Main函数
````csharp
被隐藏在 app.g.i.cs 这个文件中，且默认在解决方案管理器中看不到这个
````
## WPF应用程序的启动过程
````csharp
1.C#的程序都是从Mian函数启动，WPF的Main函数位于app.g.i.cs这个文件中

		我们找到Main函数：
		public static void Main() {
            QQL.App app = new QQL.App();	//实例化App类app
            app.InitializeComponent();		//初始化
            app.Run();						//运行app
        }
        
 2.紧接着CLR会在App.xaml中找到应用程序的启动窗体
 		
        如：
        <Application x:Class="QQL.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
   		 <Application.Resources>
         
         我们发现，StartupUri属性的属性值，即决定了程序启动的初始页面
````
## 启动应用自动隐藏主窗体
````csharp
protected override void OnShown(EventArgs e)
        {
            base.OnShown(e);
            Visible = false;
            Opacity = 1;
        }
````
## 注册全局热键
````csharp
步骤1 新建一个类 HotKey.cs

//如果函数执行成功，返回值不为0。 
        //如果函数执行失败，返回值为0。要得到扩展错误信息，调用GetLastError。 
        [DllImport("user32.dll ", SetLastError = true)]
        public static extern bool RegisterHotKey(
                IntPtr hWnd,                                 //要定义热键的窗口的句柄 
                int id,                                           //定义热键ID（不能与其它ID重复）                       
                KeyModifiers fsModifiers,       //标识热键是否在按Alt、Ctrl、Shift、Windows等键时才会生效 
                Keys vk                                           //定义热键的内容 
                );

        [DllImport("user32.dll ", SetLastError = true)]
        public static extern bool UnregisterHotKey(
                IntPtr hWnd,                                 //要取消热键的窗口的句柄 
                int id                                             //要取消热键的ID 
                );

        //定义了辅助键的名称（将数字转变为字符以便于记忆，也可去除此枚举而直接使用数值） 
        [Flags()]
        public enum KeyModifiers
        {
            None = 0,
            Alt = 1,
            Ctrl = 2,
            Shift = 4,
            WindowsKey = 8
        }
 
 步骤2 重写WndProc方法
 
 protected override void WndProc(ref Message m)
        {
            const int WM_HOTKEY = 0x0312;
            //按快捷键   
            switch (m.Msg)
            {
                case WM_HOTKEY:
                    switch (m.WParam.ToInt32())
                    {
                        case 100:
                            if (isShow == -1)
                            {
                                isShow = -isShow;
                                Show();
                            }
                            else
                            {
                                isShow = -isShow;
                                Hide();
                            }
                            break;
                    }
                    break;
            }
            base.WndProc(ref m);
        }
 
 步骤3 使用RegisterHotKey(Handle, 100, KeyModifiers.Ctrl, Keys.NumPad0);
````