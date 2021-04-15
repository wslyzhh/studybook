## **Web Deploy** **服务器安装设置与使用**

------

Visual studio 直接发布项目到远程服务器

**一、服务器的安装设置**

1、在windows server上确保IIS安装了【管理服务】这个功能。方法是在【服务器管理器】=>【管理】=>【添加角色和功能】=>【下一步】=>【基于角色或基于功能的安装】=>【下一步】=>【从服务器池中选择服务器】=>【下一步】=>【Web服务器(IIS)】下面。如果没有安装就安装下。

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png)

2、下载Web Deploy http://www.iis.net/downloads/microsoft/web-deploy

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

3、安装Web Deploy 选【自定义安装】=>【整个功能将被安装在本地硬盘上】=>【下一步】直到完成

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image003.png)

4、设置要使用Web Deploy的网站，选中IIS下的网站在右边双击【IIS 管理器权限】=>点击右边的【允许用户…】=>选择用户类型【Windows(W)】=>【选择(E)…】=>【高级(A)…】=>【立即查找(N)】=>Administrator=>【确定】=>【确定】=>【确定】

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image005.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image007.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image008.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image009.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image010.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image011.png)

**二、****Web Deploy** **的使用**

1、打开解决方案，右击网站项目，弹出的菜单中点击【发布(B)…】选项。

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image012.png)

2、设置配置文件，在 【发布Web】窗口中【新建自定义配置文件…】=>输入【配置文件名称(N)】=>【确定】=>【下一步】

3、设置连接

【发布方法(M)】选择Web Deploy =>

【服务器(E):】填写服务器的IP地址 =>

【站点名称(T):】填写IIS中网站的名称 =>

【用户名(N):】填写windows 服务器的远程登录账号  =>

【密码(W):】填写windows 服务器的远程登录密码 =>

【目标URL(L):】填写网站的URL例如[http://xxx.xxxxxx.xxx](http://xxx.xxxxxx.xxx/) =>

点击 【验证连接(V)】按钮 =>证书出错点击【接受】按钮 =>【下一步】

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image013.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image014.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image015.png)

4、设置，

【配置(C):】选择Release =>

数据库，如果不想使用本地的web.config中的数据库连接串覆盖服务器上的，那么这里的【在运行时使用此连接字符串(更新目标web.config)】不要勾上！=>

【下一步】=>【发布(P)】

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image016.png)

 

![img](file:///C:/Users/18liyz/AppData/Local/Temp/msohtmlclip1/01/clip_image017.png)

 

来自 <https://www.cnblogs.com/yelanggu/p/9046589.html> 