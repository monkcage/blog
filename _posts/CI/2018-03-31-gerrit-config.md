---
layout: post
title: gerrit配置
category: CI
tags: [CI, gerrit]
---

[toc]

### Gerrit配置
#### 1 Gerrit修改背景颜色

###### 1.1 方法及步骤
- cd /review_site/etc
- vim gerrit.config
```
[theme]
        backgroundColor = FCFEEF
        textColor = 000000
        trimColor = D4E9A9
        selectionColor = FFFFCC
        topMenuColor = D4E9A9
        changeTableOutdatedColor = F08080
[theme "signed-in"]
        backgroundColor = FFFFFF
[theme "signed-out"]
  	backgroundColor = 00FFFF
```
- 重启gerrit

###### 1.2 tips
    使用双引号，不适用单引号，单引号重启gerrit会出错

#### 2 Gerrit权限
**gerrit中权限控制是基于群组的. 每个用户有一个或者多个群组, 访问权限被赋予这些群组.访问权限不能赋予个人用户.**

###### 2.1 系统组
gerrit系统默认自带群组
- Anonymous Users
- Change Owner
- Project Owners
- Registered Users

|组别|内容|
|:--:|:--:|
|Anonymous Users|所有用户都是匿名用户，都继承Anonymous Users所有访问权限.|
|change Owner|访问权限在Change范围内有效|
|Project Owners|访问权限在Project范围内有效|
|Registered Users|所有在页面上登录成功的用户都会自动注册成gerrit用户|

#### 3. Gerrit冲突
##### <font color="red">3.1 merge conflict</font>

解决方法：
```
cd ~/projects/pan   #切换到pan项目  
git branch   ＃查看分支情况  
git checkout master  #选择分支  
git fetch origin  #fetch与pull的区别，自己再搜吧～  
git rebase origin/master  #查看有“CONFLICT (content): ”的地方，手工解决冲突后，下一步  
git add dev/controller/web/index.php #这只是一个举例，即要先add操作  
git rebase --continue  
git push origin HEAD:refs/for/master    #OK了  
```

tips:
- 以上是master分支出现merge confict的冲突，如果是分支B002 出现conflict，命令改为"git rebase origin/B002"

#### 4. Gerrit ldap认证
ldap地址：
- ldap://xxx.com

ldap认证错误：
description：
> [2017-10-16 17:10:11,702]  ERROR com.google.gerrit.server.auth.ldap.LdapRealm : Cannot query LDAP to authenticate user
   2 javax.naming.CommunicationException: lggad03-dc.china.huawei.com:389 [Root exception is java.net.UnknownHostException: xxx.com]
- 服务器主机没法找到

#### 5. Gerrit数据库浅析
![](assets/markdown-img-paste-20171019085902110.png)

> - accounts 和 account_groups分别存储用户信息和分组信息，主键为account_id和group_id
> - account_group_members存储群组成员，外键为account_id和group_id
> - account_group_names表存储群组名字，主键为name,外键为group_id
>- account_ssh_keys表存储用户SSH公钥, 外键为account_id
>- account_external_ids表存储用户敏感信息, 主键为external_id, 外键 为account_id
>- schema_version主要存储gerrit schema版本号
>- system_config主要存储site_path，如/home/gerrit/review_site

account_external_ids表中主键为external_id
> - gerrit:<username>　#This is the username to sign in into the Gerrit WebUI.
> #用于web页面登录
> - username:<username> #This is the username used for the SSH authentication.
> #用于fetch/push代码, SSH认证由用户保存的公钥验证，所以不需要密码
mailto:<email>
> #Scheme used to represent only an email address
> #格式用于存储email地址
> #如果配置认证方式为ldap, gerrit提示用户输入用户名和密码，然后它通过对配置的ldap.server执行一个简单的绑定验证，这个时候gerrit不需要存储密码
> - #如果配置认证方式为HTTP, 使用gerrit最方便的协议是SSH,如果使用HTTP协议，需要Settings -> HTTP Password生成认证密码，提交代码时不通过代理服务器，直接连接监听地址
> #密码保存在external_id格式为username:<username>的记录下
> #所以external_id格式为username:<username>用于提交代码时HTTP和SSH认证

#### 6.如何获取一个工程的配置文件(project.config)

```git
mkdir temp
cd temp
git init
git remote add origin ssh://admin@remote.site.com:29418/$(project name)
git fetch origin refs/meta/config:refs/remotes/origin/meta/config
git checkout meta/config
```

##### 6.1 添加label "Verified"

- 编辑project.config
```
[label "Verified"]
    function = MaxWithBlock
    value = -1 Fails
    value =  0 No score
    value = +1 Verified
```
将修改提交，权限控制就增加了一项"Verified",使用管理员账户提交


#### 7. gerrit创建CI用户
cmd 

    cat .ssh/id_rsa.pub | ssh -p 29418 192.168.1.1 gerrit create-account --group "Non-Interactive Users" --ssh-key - jenkins_build

    cat .ssh/id_rsa.pub | ssh -p 29418 192.168.1.1 gerrit create-account --group "Administrators" --full-name remote_gerrit --email remote_gerrit@xxx.com --http-password passwd remote_gerrit

    cat .ssh/id_rsa.pub | ssh -p 29418 192.168.1.1 gerrit set-account --http-password passwd jenkins_build

以.ssh下面的公钥在'192.168.1.1'上创建了一个名为"jenkins_build"的账户，所加入的组为"Non-Interactive Users"
可以用'ssh -p 29418 jenkins_build@192.168.1.1'测试所创的用户是否可以正式ping通

<font color='red'>tips:Caller must be a member of the privileged 'Administrators' group, or have been granted the 'Create Account' global capability.</font>

##### 7.1如何在Jenkins上配置可以连接工程
- 1. 创建一个credential，名称对应
![](assets/7.1-001.png)

- 2. 在系统配置的个gerrit-trigger中配置好这几项
![](assets/7.1-002.PNG)

-3. 工程配置
![](assets/7.1-003.PNG)

##### 8.1 Jenkins 启动命令
- 进入到jenkins所在目录，'java -jar jenkins.war'
- `net start jenkins`

### tips
####1. 配置gerrit时，<font color="red">The authenticity of host '[192.168.1.1]:29418 ([192.168.1.1]:29418)' can't be established.</font>
RSA key fingerprint is 9e:15:5c:7d:c7:ec:ba:68:fd:98:10:42:31:e0:5f:ba.
Are you sure you want to continue connecting (yes/no)? yes

<font color='dark green'>原因及解决办法:</font>
> 执行"ll .ssh"时发现ssh文件夹和内容的所属人所属组为nobody nogroup,使用chown命令将其改为当前用户 ,命令实例： chown -R xxx:xxx .ssh
![](assets/markdown-img-paste-20171016160627887.png)
改变后：
![](assets/markdown-img-paste-20171016160526385.png)
如果出现
![](assets/markdown-img-paste-20171016160831209.png)
是因为私钥id_rsa权限太高，将其设为600

#### 2. 执行init-epipe-env-new.sh出现curl错误
<font color='dark green'>原因及解决办法:</font>
可能因为访问的地址已经变更了
commit-msg


#### 3. Permission Denied(publishkey)
首先确认：
- 1. Verify that you are using the correct username for the SSH command and that it is typed correctly (case sensitive). You can look up your username in the Gerrit Web UI under 'Settings' → 'Profile'.
- 2. Verify that you have uploaded your public SSH key for your Gerrit account. To do this go in the Gerrit Web UI to 'Settings' → 'SSH Public Keys' and check that your public SSH key is there. If your public SSH key is not there you have to upload it.
- 3. Verify that you are using the correct private SSH key. To find out which private SSH key is used test the SSH authentication as described below. From the trace you should see which private SSH key is used.


检查.ssh文件的权限，参考tips1

解决问题参考：
- 参考官方文档，去官方文档(http://192.168.1.1:8080/Documentation/error-messages.html)搜索相关内容，自己百度
- gerrit数据库修改数据后，记得重启gerrit

### Gerrit 添加一个新的分支

1. 打开 Gerrit 网页，进入 Project->List 页面。选中一个工程 `Project Name`，这里以 V250_Cpipe举例。 
2. 进入 Branches 页面，在 Branch Name 中填入分支名，这里设置为 V250_Test；Initial Revision 中填入初始的版本号，一般以 master 分支的版本号为准，也可以填入其他的分支。
3. 进入 Access 页面，点击 Edit 来编辑 V250_Cpipe 的权限，拉到页面最下，点击 Add Reference 来添加一个新分支的权限 _refs/for/V250_Test_ ，默认是 _refs/for/*_ ，可以控制所有分支的权限，但是这样权限控制太粗放不推荐。这里可以通过Add Permission 添加各种权限！


### Gerrit REST API

1. 开启 http 登录权限

    进入 Gerrit 安装目录'/home/gerrit/review_site/etc'（此处以192.168.1.1举例），打开 gerrit.config。在[auth]栏下添加 'gitBasicAuth = true'。
        [auth]
            gitBasicAuth = true
    保存修改后重启 gerrit。

2. 测试修改是否成功。

    在 Gerrit Triger 的高级设置中，选中 `Use REST API` 填入用户名密码，选中 `Enable Code-Review` 和 `Enable Verified`。
    然后点击 `Test REST Connection` 按钮测试 REST API 是否正常开启。
    ![REST_API](assets\REST_API.png)

        curl http://192.168.1.1:8080/path/to/api/
        curl -X PUT http://192.168.1.1:8080/path/to/api/
        curl -X POST http://192.168.1.1:8080/path/to/api/
        curl -X DELETE http://192.168.1.1:8080/path/to/api/

        curl --digest --user user:5pbWAiOq3U1JMbIToKm61FFhcZ445lkqWFQdk4g1fg http://192.168.1.1:8080/a/path/to/api/
        curl --digest -n http://192.168.1.1:8080/a/path/to/api/
