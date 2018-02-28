# curly-telegram
svn配置
鉴于在搭建时，参考网上很多资料，网上资料在有用的同时，也坑了很多人

本文的目的，也就是想让后继之人在搭建svn服务器时不再犯错，不再被网上漫天的坑爹作品所坑害，故此总结

/******开始*********/

系统环境：Centos 6.5

第一步：通过yum命令安装svnserve，命令如下：

>yum -y install subversion

此命令会全自动安装svn服务器相关服务和依赖，安装完成会自动停止命令运行

若需查看svn安装位置，可以用以下命令：

>rpm -ql subversion

第二步：创建版本库目录（此仅为目录，为后面创建版本库提供存放位置）

选择在var路径下创建版本库，当前处于根目录下，一次性创建如下：

>mkdir /var/svn/svnrepos

第三步：创建svn版本库

在第二步建立的路径基础上，创建版本库，命令如下：

>svnadmin create /var/svn/svnrepos/xxxx   （xxxx为你预期的版本库名称，可自定义）

创建成功后，进入xxx目录下

>cd /var/svn/svnrepos/xxxx

进入目录，可以看见如下文件信息：



第四步：配置修改

进入已经创建好的版本库目录下，也就是前文说创建的xxxx

进入conf

>cd /var/svn/svnrepos/xxxx/conf

conf目录下，一共存放三份重要的配置文件，如下：



 

authz：负责账号权限的管理，控制账号是否读写权限

passwd：负责账号和密码的用户名单管理

svnserve.conf：svn服务器配置文件

细节修改如下：（希望大家严格按照以下信息，不用参考网络上其他资料）

修改authz文件信息，如下：

>vi authz

在文件内容的末尾，添加如下：



 

只需在末尾添加，无需在文件其他部分修改和添加任何东西（请忽略groups被我马赛克的地方，那其实也是条无用的记录，我忘记删掉而已），末尾内容如下：

[\]

账号1 = rw

账号2 = rw

。。。。。

rw表示赋予此账号可读写的权限，请注意[]中的斜杠，一定是反斜杠，有些教程说，需添加版本库名称在括号内，我直接建议就这写，这样写允许访问的权限更大，避免一些错误

修改passwd文件信息

>vi passwd

账号密码文件无需做修改，也是直接将账号和密码信息追加到文件中即可，注意格式为：

账号 = 密码

例如：admin = 123456

修改svnserve.conf（重要）

vi svnserve.conf

原始文件内容，都被注释掉的，我们只需要去掉4条指定内容前注释即可，如下：



 

大多数网络资料，都会让大家将authz-db = authz这条给去掉注释，经过我本人多次被坑经验，此条去掉后，虽然svn服务器可以连接，但一直会提示“认证失败”，注释掉即可正常

还有多数资料会让大家在realm = My First Repository处填写服务器ip，经过测试，填写后并无什么用处，所以大家去掉注释即可，无需做任何修改

到此，配置已经全部完成，账号信息已经添加成功

第五步：防火墙开启

多数情况下服务器安装完成，配置完成后，无法连接svn服务器，均是防火墙问题，大家按照如下3条命令逐一执行即可

>/sbin/iptables -I INPUT -p tcp --dport 3690 -j ACCEPT

>/etc/init.d/iptables save

>service iptables restart

执行结果如下图：



 

六：启动svn服务器

在跟目录下，执行如下命令：

>svnserve -d -r /var/svn/svnrepos

启动成功后，可用ps -aux查看服务启动是否成功

七：客户端访问svn服务器

在windows客户端，输入地址：svn://ip地址:3690/xxxx   （iP地址为你linux的ip，xxxx为前文创建的版本库名称,3690为svn默认端口）

弹出输入用户名和密码，输入即可访问

到此，Linux下svn服务器搭建就总结完毕，感谢大家的阅读
转自：https://www.cnblogs.com/mymelon/p/5483215.html
