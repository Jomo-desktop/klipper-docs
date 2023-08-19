# 系统安装与配置

# 1. 系统镜像烧录

## 1.1 烧录前的准备

**需要准备的东西：**

1. SD卡一张（>8GB）

2. 读卡器

3. 系统镜像

4. 工具软件请前往FLY官方群文件夹下载：Fly-Gemini系列；l 需要用到的软件有：balenaEtcher（fly群中文件名为系统镜像写入工具）、putty、WinSCP

5. [系统镜像](/introduction/downloadimg.md "点击即可跳转") 

**注：其他非FLY的上位机请勿使用FLY的专属镜像，否则会损坏上位机！！！**

**下载需要用到的工具和文件，国内用户请前往QQ群下载**

* [工具](https://drive.google.com/drive/folders/1llH-lq-WsbIdwkmLL51n3OHo5dNNpcPy)

## 1.2 内存卡镜像烧录

1. 先安装好软件“balenaEtcher”，也可以用其他的(Win32DiskImager)等工具
2. SD卡插入读卡器，将读卡器插到电脑USB接口
3. 打开软件，点击“**Flash from file**”选择前面下载的系统镜像文件

<img src="../../images/boards/fly_pi/etcher.png" alt="etcher" style="zoom:80%;" />

4. 点击**“Select target**”选择SD卡，大小和SD卡容量差不多的就是，或进入资源管理器查看SD卡盘符

   <img src="../../images/boards/fly_pi/etcher2.png" alt="etcher2" style="zoom:80%;" />

   5. 点击 “**Falsh**” ,出现进度条及“**Falshing…**”时开始写入系统镜像到SD卡。等待大约十多分钟，等待写入完成。

      

   6. 至此已经完成FLY镜像的写入，但是请勿拔出内存卡，还需要修改boot盘中的FLY_config文件，具体修改方法请查看：[FLY_Config](/board/fly_pi/FLY_π_fly_config.md "点击即可跳转")
   
   ## 1.3 使用USB烧录M2WE
   
   >[!TIP]
   >如果使用了内存卡给M2WE烧录后无需在使用此方法烧录镜像

​    使用USB烧录:[USB烧录M2WE](/board/fly_gemini_v3/USBm2we.md "点击即可跳转")

# 2. FLY_Config 的使用

* FLY-Config用来简化系统配置等，适用于Klipper全家桶，免去进入ssh写命令的麻烦
* 只能在FLY定制Armbian系统运行
* 对初学者友好
* 直接配置Klipper及周边

## 2.1 打开 FLY_Config

1. 如果您已经制作好SD卡，请使用读卡器连接到电脑。此时电脑应该会弹出资源管理器并且有一个“**可移动磁盘BOOT**” .如果未出现，请重新拔下 sd 卡，再插入电脑

   eMMc进入BOOT盘方法，请参考：[通过usb为emmc烧录系统镜像](/board/fly_pi/FLY_π_description1?id=_2-通过usb为emmc烧录系统镜像)

   ![open_boot](../../images/boards/fly_pi/open_boot.png)

   

2. 打开BOOT盘下的FLY-Config.conf

   ![open_flyconfig](../../images/boards/fly_pi/open_flyconfig.png)

## 2.2 可用配置

* 配置主板型号（仅限Gemini系列，其他系列默认即可）
* 指定Klipper的控制网页
* 配置WIFI
* 配置Klipperscreen

## 2.3 连接WiFi

   找到WIFI配置，将WIFI处的flase改成true，并将WiFi名称和密码填写上，保存，插回上位机。等待几分钟后，进入路由器后台管理界面查看IP地址。
<img src="../../images/boards/fly_pi/connect_wifi.png" alt="connect_wifi" style="zoom:60%;" />

## 2.4 其他可用配置

### 2.4.1 选择Klipper的控制网页

找到下图所示选项，将``UI=fluidd``修改为``UI=mainsail``，则可将控制网页由``fluidd``切换为``mainsail``。

![config1](../../images/boards/fly_pi/config1.png)

### 2.4.2 Klipperscreen的配置

找到下图所示选项，将``klipperscreen=flase``修改为``klipperscreen=true``，则可启用klipperscreen屏幕。

![config1](../../images/boards/fly_pi/config1.png)

另外还需要指定使用的屏幕类型才能够正常使用！！！按下图中的提示修改为对应的配置即可。

![hdmi](../../images/boards/fly_pi/hdmi.png)

# 3. SSH连接

* 请提前准备好Putty或其他SSH终端工具

## 3.1 串口连接

* 使用USB Type-c线材将FLY-π与电脑连接

![typec_tossh](../../images/boards/fly_gemini_v3/typec_tossh.png)

* 如果主板与电脑连接正常，打开设备管理器，就可以看到CH340的端口了

  ![ssh2](../../images/system/ssh2.png)

* 打开Putty，进行如下设置,端口填写前面在设备管理器看到的端口

![ssh3](../../images/system/ssh3.png)

* 一切正常的话就可以看到启动画面了

  <img src="../../images/system/ssh4.png" alt="ssh4" style="zoom:70%;" />

* 等待片刻，出现这个画面就是启动了

<img src="../../images/system/ssh5.png" alt="ssh5" style="zoom:70%;" />

* 查看IP，如果配置了FLY-Config中的WIFI则开机后就会自动连接
* 在终端中输入命令```ip a|grep inet```回车
* 在返回内容中找到与你路由器同段的IP即可访问

> 如果是主板启动很久后才连接的SSH那么请连接后按几下回车键
>

?> 使用typec连接到ssh时，登录的为root账户，建议切换到普通账户后再进行操作！！！输入`su fly`命令，切换到普通用户

* 此时会提示输入用户名，在新窗口终端中输入```fly```后回车

![ssh8](../../images/system/ssh8.png)

* 提示输入密码，输入```mellow```后回车

![ssh9](../../images/system/ssh9.png)

## 3.2 远程连接

* 如果主板已经启动且已经连接到网络，可以使用IP连接到SSH
* 打开Putty，进行如下设置，IP填写前面查看的，也可以在路由器后台查看
* 注意，Putty选择SSH而不是Serial

![ssh6](../../images/system/ssh6.png  ":no-zooom")

* 第一次连接回弹出这个窗口，点击“是”即可

![ssh7](../../images/system/ssh7.png)

* 会提示输入用户名，在新窗口终端中输入```fly```后回车

![ssh8](../../images/system/ssh8.png)

* 提示输入密码，输入```mellow```后回车

![ssh9](../../images/system/ssh9.png)

* 登录成功

* 至此，您已经完成FLY-Gemini的SSH登陆

# 4. WiFi连接

?> 请打开FLY_config配置文件，打开方式请参照[打开fly_Config](/board/fly_gemini/host/FLY_π_fly_config.md "点击即可跳转")

连接WiFi一共有两种方式

## 4.1 通过FLY_Config连接WiFi

找到WIFI配置，将WIFI处的flase改成true，并将WiFi名称和密码填写上，保存，插回上位机。等待几分钟后，进入路由器后台管理界面查看IP地址。
<img src="../../images/boards/fly_pi/connect_wifi.png" alt="connect_wifi" style="zoom: 67%;" />

## 4.2 通过SSH软件连接

使用 [串口连接](/board/fly_gemini/host/FLY_π_ssh.md "点击即可跳转") 到SSH软件后使用nmtui命令连接WiFi

1. 在SSH软件输入`sudo nmtui`命令后回车，再输入`mellow`，会出现下图所示界面

![nmtui1](../../images/boards/fly_pi/nmtui1.png)

![nmtui2](../../images/boards/fly_pi/nmtui2.png)

2. 选择`Activate a connection`后回车

![nmtui3](../../images/boards/fly_pi/nmtui3.png)

3. 进入如下界面，通过键盘上的`↑``↓`键选择要连接的WiFi后，回车，输入密码后等待连接成功

<img src="../../images/boards/fly_pi/nmtui4.png" alt="nmtui4" style="zoom:90%;" />

<img src="../../images/boards/fly_pi/nmtui5.png" alt="nmtui5" style="zoom:90%;" />

4.连接成功后如图所示

![nmtui6](../../images/boards/fly_pi/nmtui6.png)

5. 按`ESC`键退出，返回命令行界面，输入`ifconfig`，图中`192.168.2.126`即为上位机的IP地址

![nmtui7](../../images/boards/fly_pi/nmtui7.png)