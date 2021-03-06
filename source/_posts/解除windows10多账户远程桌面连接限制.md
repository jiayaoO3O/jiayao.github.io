---
title: 解除Windows10多账户远程桌面连接限制
date: 2019-04-09 09:49:39
tags: [Windows10,远程桌面]
categories: 技术
---

从上一篇文章开启[Windows的远程桌面功能](https://jiayaoo3o.github.io/2019/04/09/%E5%BC%80%E5%90%AFWindows%E7%9A%84%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E5%8A%9F%E8%83%BD/)我们说到,Windows默认情况下只能同时登录一个账号,并且手机连上电脑,电脑就会退出登录,被踢下线,这是因为,能让多个账号远程连接到一个电脑,这个是服务器版才该有的功能,你想想,多个账号连接一台电脑,那这个电脑不应该就是服务器吗?所以想要让Windows10支持多账户远程桌面连接,我们需要一点点设置.

<!-- more -->

## 创建新的账户

想要多账户连接到自己的电脑,那首先就得先创建一个新的账户,打开

> 系统设置-账户-家庭&其他用户

点击**添加一个其他用户**

![](https://i.loli.net/2019/04/09/5cac4b60a2df4.png)

这个时候就可以输入新建用户名和密码,完成账户的添加.

给该用户远程连接权限

现在这个新建的用户只是一个普通账户,并不是管理员账户,所以并没有远程连接的默认权限,我们要去允许该账户进行远程连接

> 右键我的电脑-属性-远程设置-选择用户

![](https://i.loli.net/2019/04/09/5cac0bb7bd5f1.png)

点击"添加"按钮,然后在框内输入你刚刚创建的用户名,点击"名字检查",系统就会找到这个用户

![](https://i.loli.net/2019/04/09/5cac4cfa32e64.png)

这个时候点击ok,这个用户就有了远程连接的权限了.

## 配置本地组策略

虽然现在已经有了两个账号,但是在默认情况下,A账号登录B就会被踢下来,B登录A也会被踢下来,所以现在要设置组策略

在左下角搜索框中输入gp,系统就会自动帮你找到**组策略编辑器**,打开后,依次点击:

> 管理模板-Windows组件-远程桌面服务-远程桌面会话主机-连接

1. 双击右边的**限制连接的数量**,选择**已启用**,然后在下面选择你想开启的最大连接数,一般填个3都够用了.

2. 双击右边的**将远程桌面服务用户限制到单独的远程桌面服务会话**,*这里很多的教程会建议改成**已禁用**,但是我的建议是把这里改成**已启用**!!!*

要知道为什么这么改,我们首先要知道这个设置是干嘛的.

将用户限制到单独的远程桌面服务,是指 : 如果我手机连上A账号,电脑就不能连上A账号,电脑连上了,手机就会掉线.

那为什么我们要启用这个功能?因为**这时候手机和电脑使用的是同一个A账号的资源**,举个例子,当你用手机连接电脑的A账号在远程看电影,这时候你的电脑用A账号登录,就会把手机踢下线,但是你会发现,电脑现在的界面就在播放着手机刚刚看的视频,因为这时候他们两个用的是同一个资源,所以会有一种无缝连接的感觉,远程桌面的内容,就是电脑本身的内容.

而你如果**关闭**了**将远程桌面服务用户限制到单独的远程桌面服务会话**这个功能,就会导致一个用户可以开启多个远程桌面服务会话,**手机和电脑使用的是不同的A账号资源**.这又是什么意思?

就是说,如果你电脑现在正在登录A账号,手机选择远程桌面登录A账号,**此时电脑的A账号不会被踢下线,而是两个A账号同时工作**!但是手机的A账户的资源是完全独立于电脑A账户的,电脑A账户打开了浏览器,但是手机A账户看不见,手机A账户远程打开电影,电脑A账户也看不见,两者互相独立的远程会话.

这样的问题在于,**每次退出远程桌面连接都必须手动点击"注销"按钮**,将你的远程A账户的资源清空掉,而不可以直接关闭远程桌面App,如果直接关闭App,这个单独的A账户就会显示"断开连接",然后就一直孤零零的在后台消耗资源,直到你主动连接到这个资源,然后注销.

如果你的手机退出远程桌面连接,然后又重新连接一次,**并不会连上刚刚的断开连接的A账号,而是重新分配一个全新的A账号资源给你**,这样你的电脑就有3个A账号在工作,系统资源就会被严重浪费,直接卡爆.

因此,我建议是把这个功能**启用着**,会更合适一般人.

## 开启多用户连接功能

虽然刚刚设置了最大连用户数,但是我们的系统本质上装的并不是服务器版本,所以到目前为止我们还是不能够多用户同时连接的,要去github上下载[RDPWrap](https://github.com/stascorp/rdpwrap/releases/download/v1.6.2/RDPWrap-v1.6.2.zip)这个软件(不要去csdn等地方下载来路不明的文件,直接去github原作者那下载是最好的).

解压之后我们右键选择用管理员依次运行这三个文件

- install.bat
- update.bat
- RDPConf.exe

如果你的系统版本太新,那你很可能箭头这里依然会显示[not supported]

![](https://i.loli.net/2019/04/09/5cac551aa9a44.png)

这时候[点击下载](https://github.com/stascorp/rdpwrap/files/2949950/rdpwrap.zip)这个文件,解压后将里面的rdpwrap.ini文件替换到

> C:\Program Files\RDP Wrapper

再重复刚刚的步骤应该就可以看到[fully supported]的字眼了.

如果你的系统比1089还新,或者更换了rdpwrap.ini文件仍然不支持,就需要到这个代码仓库的[issues](https://github.com/stascorp/rdpwrap/issues)里面搜索你的系统版本(也就是上面的**10.0.17763.292**字眼),一般来说都会有人贴出新系统的配置信息,你只要把这个配置信息粘贴到刚刚那个rdpurap.ini文件的末尾就行了.例如下图:

![](https://i.loli.net/2019/04/14/5cb29e742f1c9.png)

到这里,所有的步骤就都结束了,你的电脑现在可以让两个账户同时连接而不掉线了.
