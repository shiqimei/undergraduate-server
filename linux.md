# Linux基础知识

## 查看 dd 的进度
````bash
sudo watch -n 1 pkill -USR1 -x dd
````

## 排除移动
````bash
mv !(SiamFC) ./SiamFC
````

## 查看文本文件和二进制文件
````bash
cat filename # 以文本形式查看文件内容
xxd filename # 以16进制查看二进制文件内容
````
## 高亮筛选
````bash
grep --color=auto
````

## wc
````bash
wc # 统计字符数
wc -l # 仅统计行数
````
## 列出当前目录下文件夹的个数
````bash
ls -l | grep ^d | wc -l
````

## 自动挂载
````bash
sudo fdisk -l # 查看所有磁盘设备
sudo dumpe2fs -h /dev/sdb2 # 查看磁盘 UUID 信息
````

修改文件 `/etc/fstab`
````bash
# /home/lolimay/computervision
UUID=3b3fa56d-c9e6-4d38-a623-f8ccc6a70f43   /home/lolimay/computervision    ext4    defaults    0   2
````
## mv排除指定文件
````bash
mv !(ILSVRC15_branch) ILSVRC15_branch
````
## 查看当前解压进度
````bash
progress -q
````
## 压缩文件
````bash
tar -zcvf compressed.tar.gz path # 压缩文件必须先指定压缩后的文件名
````
## 懒卸载硬盘
````bash
sudo umount -l
````
## 查看当前目录剩余空间
````bash
du -ah --max-depth=0
````
## 修复 dpkg 错误
dkpg: 警告: 无法找到软件包 xxxxxx 的文件名列表文件，先假定该软件包目前没有任何文件被安装在系统里
*解决方案：*
先把这个错误列表复制保存到一个 txt 文件中，如 `dpkg_list.txt`，然后使用编辑器（我使用的是 VS Code）中的文本搜索替换（Ctrl+H）把软件包前面的文本和后面的文本删除，留下一个软件包列表。最后运行下面的命令重新安装所有发生错误的软件：
````bash
cat dpkg_list.txt | while read line; do sudo apt install --reinstall $line; done # 重新安装找不到文件名列表的文件
```
## xfce4安装主题
把下载好的主题文件夹移动到 `/usr/share/themes` 文件夹下
## ubuntu手动安装字体
````bash
mkdir -p ~/.font
# 接着将需要安装的字体移动到该目录下
fc-cache -fv # 更新字体缓存
````
## 使 ssh 支持 root 用户登录
````bash
# 注意是 sshd_config 文件而不是 ssh_config 文件
vim /etc/ssh/sshd_config # 修改或添加 PermitRootLogin yes
service ssh restart
````
## 【ssh problem】avoid being asked passphrase each time I push
````bash
ssh-add
````
## vbox无法识别USB设备
````bash
sudo apt install gnome-system-tools
````
用户和组->高级设置->用户权限->使用Virtual Box虚拟化解决方案
## coreutils viewer

查看当前核心命令的处理进度

````bash
cv -w
````

## 带进度条复制

````bash
rsync -- source dest
````

## 查看磁盘剩余空间

````bash
df -h
````

## 卸载磁盘

````bash
sudo umount /media/lolimay/phicom
````

## 挂载磁盘

````bash
sudo fdisk -l # 查看所有磁盘
sudo mount /dev/sdc1 /media/lolimay/phicom
````

## 生成任意大小的文件

````bash
dd if=/dev/zero of=50M.file bs=1M count=50
````

## 重启 DNS 服务

````bash
/etc/init.d/dnsmasq restart
````

## 路由表

gw(gateway) 网关

````bash
route add -net 172.0.0.0 netmask 255.0.0.0 gw 172.24.0.1
````

## OpenWrt端口转发

````
vim /etc/config/firewall
````

## 查看所有系统服务

````
service --status-all
systemctl status sshd # 查看 ssh 服务的运行状态
journalctl -xe # 查看服务器启动失败错误日志
journalctl -u sshd # 单独查看 ssh 的日志
journalctl --since "15:40:00" # 查看从 15:40:00 之后的日志
````

## 启动 flatpak 版本的深度取色器

````shell
flatpak run --command=deepin-picker org.deepin.flatdeb.deepin-picker
````

## 系统目录

* `/etc/skel/` `该目录用于存放新用户配置`
    当我们损坏了 `home` 目录下的配置文件时，可以通过复制该目录下的相应文件以恢复初始状态。

## 安装 nodejs 和 npm

````shell
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
nvm install stable
nvm ls
nvm ls-remote
````

## 计划任务

````
service cron status #查看cron服务是否已在运行
crontab -l #列出该用户的计时器设置
crontab -e #编辑该用户的计时器设置；
crontb -r #删除该用户的计时器设置；
crontab -u<用户名称> #指定要设定计时器的用户名称

*/1 * * * * sh ~/Scripts/hexo.sh #每分钟执行一次 hexo.sh
````

## 选择文本编辑器

````
select-editor
````

## 查看端口号

````
lsof -i :8080 #查看8080端口
ps -ef | grep node #列出所有node进程
kill -9 <pid> #结束特定node进程
netstat -apn | grep 888 #检测本机端口是否被占用
netstat -ntpl #显示本机所有已开放的端口
netstat -apn | egrep 'tcp|udp|tcp6|udp6' #查看tcp/udp/tcp6/udp6端口
````

## 修复NTFS磁盘只读

````
sudo apt install ntfs-config
df -h #查看只读分区的文件系统
umount /dev/设备
sudo ntfsfix /dev/设备
mount /dev/设备
````

## 硬盘健康状态检测

````
sudo fdisk -l #查看所有磁盘
sudo smartctl -i /dev/sdb1 #查看硬盘基本信息
sudo smartctl -H /dev/sdb1 #查看硬盘健康状态
````

## vbox无法识别U盘

````
sudo usermod -G vboxusers -a lolimay #将 lolimay 加入 vbox用户组
````

## tar 打包当前目录下所有隐藏文件（排除 ../ 和 ./）

````
tar czvf test.tar.gz  .[!.]*   #压缩当前文件夹下隐藏文件排除两个隐藏文件夹"."和“..”
````

## scp传送文件

````
scp -P 12582 -r 吴恩达机器学习/ sys.westery.cn:/home/lolimay/Downloads/
````

## wget不保存文件

````bash
wget --output-document=/dev/null http://www.domain.com
````

## 查看文件占用情况

````bash
du -sh * # 查看所有普通目录占用的空间大小
du -sh .[!.]* # 查看所有隐藏目录占用的空间大小
````

## 修改 grub 启动等待时间

修改 `/etc/default/grub` 中 `GRUB_TIMEOUT` 的值，接着 `sudo update-grub` 重启后生效。

## rm误删修复

````bash
sudo apt install foremost
vim /etc/foremost.conf # 增加新的文件类型
````