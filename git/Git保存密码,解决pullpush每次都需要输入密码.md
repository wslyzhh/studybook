## Git保存密码,解决pull/push每次都需要输入密码

------

**1.方法一** 

  1.1 创建文件存储GIT用户名和密码

  在%HOME%目录中，一般为C:\users\Administrator，也可以是你自己创建的系统用户名目录，反正都在C:\users\中。文件名为.git-credentials,由于在Window中不允许直接创建以"."开头的文件，所以需要借助git bash进行，打开git bash客户端，进行%HOME%目录，然后用touch创建文件 .git-credentials, 用vim编辑此文件，输入内容格式：

```
touch .git-credentials
vim .git-credentials
https://{username}:{password}@github.com
```

  1.2 添加Git Config 内容（也可以直接执行1.2，再操作一下pull操作输入密码之后就不用了）

   进入git bash终端， 输入如下命令：

```
git config --global credential.helper store
```

  执行完后查看%HOME%目录下的.gitconfig文件，会多了一项：

```
[credential]helper = store
```

  重新开启git bash会发现git push时不用再输入用户名和密码

  

**2.方法二**

 

  2.1 添加环境变量

  

  在windows中添加一个HOME环境变量，变量名:HOME,变量值：%USERPROFILE%

  

  2.2 创建git用户名和密码存储文件

  

  进入%HOME%目录，新建一个名为"_netrc"的文件，文件中内容格式如下：

```
machine {git account name}.github.comlogin your-usernmaepassword your-password
```

  重新打开git bash即可，无需再输入用户名和密码