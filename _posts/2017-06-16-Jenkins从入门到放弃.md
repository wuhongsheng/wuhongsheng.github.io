---
layout: post
title:  "Jenkins从入门到放弃"
date:   2017-06-16 08:14:54
categories: jenkins
tags:  jenkins CI
author: WHS
---

* content
{:toc}

[Jenkins](https://jenkins.io/)安装及使用

Jenkins是基于Java开发的一种持续集成工具，用于监控持续重复的工作，功能包括：
* 持续的软件版本发布/测试项目。
* 监控外部调用执行的工作。
* 对于Android来说，可以自动打包、Android lint 检查、自动测试等。



***

### 参考资料

[jenkins git gradle android自动化构建配置](http://www.cnblogs.com/wnfindbug/p/5784476.html)

### 准备及安装(以Mac为例)

* JDK、SDK、GRADLE配置(包括环境变量)

* [安装Tomcat](http://wuhongsheng.top/2017/06/14/Tomcat安装从入门到放弃/)

* 下载[Jenkins.war](https://jenkins.io/doc/)并复制到Tomcat webapps目录下

* 配置Jenkins环境变量(启动web容器之前)
  1.Windows
  Windows环境中，Jenkins主目录默认在C:\Documents and Settings\AAA\.jenkins 。
  可以通过设置环境变量来修改，例如： JENKINS_HOME=C:\jenkins，然后重新启动jenkins。
  

* Jenkins unlock

打开主目录找到改文件(windows用notpad++打开password文件)

![](http://ooxw95lkz.bkt.clouddn.com/jenkins_unlock.jpg)



* 配置Plugin

* 配置用户及权限（如果忘记这步会导致下次登陆验证失败，解决方法见下文）一定要先配置，后面配会比较麻烦

* 系统配置
  1.配置环境变量

    * [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

    * AndrodSDK

      1.[下载]()

    * Gradle
      1.[下载](http://services.gradle.org/distributions)
      2.设置环境变量
      3.
  2.配置全局Tools

  3.配置邮件服务

    * 在系统设置中找到Jenkins Locaction项填入Jenkins URL和系统管理员邮件地址，系统管理员邮件地址一定要配置，否则发不了邮件通知。因为邮件通知都是由系统管理员的邮箱发出来的。

    ![](http://img.blog.csdn.net/20161103193043944?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

    * 找到邮件通知项，填入SMTP服务器信息及用户名、密码等认证信息。

    ![](http://ooxw95lkz.bkt.clouddn.com/jenkins%E9%82%AE%E4%BB%B6%E9%80%9A%E7%9F%A5%E9%85%8D%E7%BD%AE.png)

    * 配好以后勾选“通过发送测试邮件测试配置”

    ![](http://img.blog.csdn.net/20161103193124369?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

    * 填入接收测试邮件的地址，点击“Test configuration”。如果配置正确就会在下面显示Email was successfully sent

    ![](http://img.blog.csdn.net/20161103193149854?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


  2.配置Gradle环境
    * [下载地址](http://services.gradle.org/distributions)

    * 上传到linux并解压： unzip gradle-2.14.1-all.zip 

    * 配置环境变量：

    ```
    export GRADLE_HOME=/home/cfjekins/gradle-4.0 （gradle解压后的目录）
    export PATH=$PATH:$GRADLE_HOME/bin
    ```
　 
    * source命令使配置生效：source .bash_profile(source 文件名)

    * 检验配置是否生效：echo $GRADLE_HOME



  3.配置Java环境  

    * 配置环境变量：

    ```
    export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_79.jdk/Contents/Home
    export PATH=$JAVA_HOME/bin:$PATH:
    ```

　 



### 升级



***

### 新建和配置项目


[教程](http://wangkuiwu.github.io/2015/08/07/jenkins-02/)


* 导出编译文件

![](http://ooxw95lkz.bkt.clouddn.com/jenkins-out.png)

* 构建

![](http://ooxw95lkz.bkt.clouddn.com/jenkins-build.png)

#### 常见问题及解决方法

**登陆失败**

提示登陆信息无效

**解决方法**

* ```cd /Users/[username]/.jenkins/```


* 打开config.xml文件并修改配置信息(windows系统可能会保存在这个路径下C:\Windows\System32\config\systemprofile)

<pre class="prettyprint lang-xml">

 &lt;!--修改成false--&gt;
 &lt;useSecurity&gt;true&lt;/useSecurity&gt;
 &lt;!--删除 &lt;/authorizationStrategy&gt;和&lt;/securityRealm>,重启jenkins，重新打开jenkins即可--&gt;
 &lt;authorizationStrategy class="hudson.security.FullControlOnceLoggedInAuthorizationStrategy">
 &lt;denyAnonymousReadAccess&gt;true&lt;/denyAnonymousReadAccess&gt;
 &lt;/authorizationStrategy&gt;
 &lt;securityRealm class="hudson.security.HudsonPrivateSecurityRealm"&gt;
   &lt;disableSignup&gt;true&lt;/disableSignup&gt;
   &lt;enableCaptcha&gt;false&lt;/enableCaptcha&gt;
 &lt;/securityRealm&gt;
 
</pre>

 **SDK许可问题**

 **解决办法**

 * Linux

```
mkdir "$ANDROID_HOME/licenses"
echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
```
 * Windows

```
mkdir "%ANDROID_HOME%\licenses"
echo |set /p="8933bad161af4178b1185d1a37fbf41ea5269c55" >"%ANDROID_HOME%\licenses\android-sdk-license"
```



* 配置运行权限

``
chmod a+x startup.sh
chmod +x catalina.sh
``

* 配置环境变量

``
open -e .bash_profile
export PATH=${PATH}:/usr/local/apache-tomcat-9.0.0.M21/bin
``

* 启动Tomcat

``
startup.sh
``





