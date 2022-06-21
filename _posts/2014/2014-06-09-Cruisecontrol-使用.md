---
layout: post
title: Cruisecontrol 使用
categories: Cruisecontrol
tags: CC
---

CruiseControl ：简称 CC ，持续集成工具(集成测试工具)，主要提供了基于版本管理工具 ( 如 CVS、VSS、SVN) 感知变化或每天定时的持续集成，并提供持续集成报告、 Email 、 Jabber 等等方式通知相关负责人，其要求是需要进行日构建的项目已编写好全自动的项目编译脚本 ( 可基于 Maven 或 Ant) 。

cruisecontrol+svn：在这之前要确定你的svn终端命令可以用，要先下个subversion(版本控制服务器)

## Subversion和TortoiseSVN安装与配置 

Subversion为版本控制软件的服务器端。 

TortoiseSVN为版本控制软件的客户端。 

1。下载Subversion与TortoiseSVN。

Subversion的地址：<http://subversion.tigris.org/servlets/ProjectDocumentList?folderID=91>

我选择的是[Setup-Subversion-1.5.3.msi]Windows安装程序包。

TortoiseSVN的地址：<http://tortoisesvn.net/downloads>

[TortoiseSVN-1.5.5.14361-win32-svn-1.5.4.msi]Windows安装程序包。

如果不是安装subversion版本控制的话（就是安装服务器），只用在环境变量里面加入D:\subversion\bin就好了，这时可以检测一下：在终端里面输入svn，看可不可以，如果要安装subversion，见：<http://blog.csdn.net/ieclipse/archive/2008/12/17/3539706.aspx>

下面可以开始cruisecontrol的配置了，下个cruisecontrol的包，最好下cruisecontrol-bin-2.3.1.zip(bin包)，然后解压：这里比如把要监控的对像放到cruisecontrol下的projects下的myrobot下面，先check out下一份下来然后：

用编辑器打开cruisecontrol.sh或cruisecontrol.bat ，在下面这段代码中修改端口号，将8080修改为7070或其他：

     $LIBDIR" -jar "$LAUNCHER" $@ -jmxport 7000 -webport 7070 -rmiport 1099 &

修改curisecontrol的config.xml:

    <cruisecontrol>   
     <project name="myrobot">

         <listeners>
             <currentbuildstatuslistener file="logs/${project.name}/status.txt"/>
         </listeners>

         <bootstrappers>
         </bootstrappers>


         <modificationset quietperiod="30">
             <svn localworkingcopy="projects\myrobot"/>
         </modificationset>
       <!-- 这里设置interval="300"，这意味着CruiseControl每隔5分钟使用<modificationset>定义的任务检查一次代码库。如果本地跟版本库不一样的话就执行schedule，如果一样的话就不执行   -->
         <schedule interval="300">
             <ant anthome="apache-ant-1.7.0" buildfile="projects/${project.name}/build.xml" target="force.deploy"/>
         </schedule>

         <log>
             <merge dir="projects/${project.name}/logs"/>
         </log>

         <publishers>
             <htmlemail
                 buildresultsurl="http://127.0.0.1:7070/cruisecontrol/buildresults/myrobot"

                (上面的url地址可以先进入http://127.0.0.1:7070/cruisecontrol查看你的项目的地址)
                 mailhost="smtp.gmail.com" username="aaa@gmail.com" password="123456"
                 returnaddress="aaa@gmail.com" skipusers="true" subjectprefix="[Cruise Control]"
                 logdir="D:\curisecontrol\cruisecontrol-bin-2.8.4\projects\myrobot\logs" >
                 <always address="aaa@goodhope.net"/>
             </htmlemail>
         </publishers>

     </project>
    </cruisecontrol>

要监控多个项目就加入多个project，

## <cruisecontrol>

`<cruisecontrol>`是配置文件的根元素，它可以拥有一个或多个<project>子元素。本例中它拥有一个项目名为frameworkProject。

## <project>

`<project>`元素是一个完整的build任务，包括检查配置管理库是否有新的修改，构建项目并发布项目构建结果。它告诉 CruiseControl构建什么，何时构建，如何构建以及如何发布构建报告。它有一个必需的属性name和一个可选属性 buildafterfailed。
       属性buildafterfailed定义了当构建失败时，是否要继续进行，缺省是"true"。

<project> 元素的子元素包 括，<bootstrappers>，<modificationset>，<schedule>，<log>，<publishers>，<dateformat> 和<plugin>，其中<modificationset>和<schedule>是必需的元素。

## <dateformat>

`<dateformat>`用于定义日期的格式，缺省格式是：MM/dd/yyyy HH:mm:ss。

## <bootstrappers>

<bootstrappers>元素是启动任务Plugin的容器，用于定义构建任务启动前需要执行的任务。常用的Plugin包括：

1.<currentbuildstatusbootstrapper>，定义一个CruiseControl的构建状态信息文件。 CruiseControl的Build Result JSP从该文件读取状态信息并显示在页面上。属性file用于指定构建状态文件目录和文件名。

    <bootstrappers>
         <antbootstrapper anthome="apache-ant-1.7.0" buildfile="projects/${project.name}     /build.xml" target="force.deploy" />
         <svnbootstrapper localWorkingCopy="projects/myrobot/" />  
    </bootstrappers>

2. <cvsbootstrapper>，用于在项目构建开始前从CVS服务器上update指定的文件。通常可以用于更新项目的构建脚本。属性 localWorkingCopy指定CVS本地工作目录，属性file指定需要update的文件名，相对于属性localWorkingCopy指定 的目录。

## <modificationset>

<modificationset>元素用于告诉CruiseControl是否需要构建项目，即配置管理库的代码是否存在更新。它拥有两个可选属性requiremodification和quietperiod。

属性requiremodification告诉CruiseControl，在配置管理库没有代码更新的情况下，是否需要构建。缺省为"true"，即没有更新则无须进行构建。

属性quietperiod告诉CruiseControl，最新一次代码提交后CruiseControl需要等待的时间（秒）。用于防止CruiseControl在开发人员提交代码时进行项目构建。缺省为"60"秒。

在本例中使用<cvs>来检查和工作目录相关的代码在CVS配置管理库是否有更新。<cvs>使用"cvs log"命令来检查最新更新工作目录和当前代码库的差异。
  
## <schedule>

到目前为止，以上的配置文件内容已经定义了CruiseControl构建什么以及何时构建。<schedule>元素告诉 CruiseControl每隔多长时间（秒）启动一次构建任务。它有一个可选的属性interval，用于定义以秒为单位的时间间隔。缺省为"300" 秒。

在本例中，属性interval设为"3600"，这意味着CruiseControl每隔一个小时使用<modificationset>定义的任务检查一次代码库。

<schedule>元素拥有三个子元素<ant>，<maven>和<parse>。

<ant>子元素告诉CruiseControl何时或每隔几次运行Ant来构建项目。

在本例中，antWorkingDir属性设定Ant的工作目录，buildfile属性设定构建脚本build file的目录。

属性multiple告诉CruiseControl每隔几次执行一次本<ant>任务。

除此之外，还可以指定Ant的运行时间（time属性），build file的target（target属性，不设定则为build file的缺省target）。

请参见CruiseControl的配置文档（位于${CruiseControl_Home}/main/docs目录下）。
  
## <log>

<log>元素设定CruiseControl日志文件的存放目录，并通过<merge>子元素指定合并什么样的XML文件（构建过程中产生的文件）到CruiseControl的日志文件中。

<merge>子元素的pattern属性定义匹配的文件名模式，缺省为".xml"；dir属性用于指定一个目录，这个目录下所有匹配模式的文件将合并到CruiseControl的日志文件中。
  
## <publishers>

<publishers>元素用于指定构建任务结束后，CruiseControl如何发布项目构建结果。项目构建结果的发布方式可以是Email，网页，复制代码库到指定的目录，或是发布代码库到FTP服务器。

在本例中，共有<currentbuildstatuspublisher>，<artifactspublisher>和<htmlemail>三个publisher。

<currentbuildstatuspublisher> publisher把下次构建的时间写入指定文件，文件名由file属性设定。

<artifactspublisher> publisher元素把项目构建产品复制到指定的目录，dir属性定义源目录，dest定义目标目录的父目录（实际目录还要加上构建时的时间戳，如：父目录/19890604203828）。

<htmlemail> publisher把构建结果以HTML格式通过Email发布。缺省情况下，HTML格式的Emai和CruiseControl Web应用的构建结果JSP页面相同。

本例中<htmlemail>的属性和子元素的作用很容易理解，更多的配置项参见联机文档。

check out下来的版本还要修改build.xml:
 
    <project name="myRobot" basedir="." default="all">
       。。。。。。。。。。。。。。。。（省略）
        <target name="force.deploy" description="force.deploy">
             <exec executable="svn">
                <arg line="update" />
            </exec>
             <antcall target="--deploy">
             </antcall>
         </target>
    </project>
    
 (机器上第一次checkout的时候输入svn的地址就行了，之后在终端update或者checkout的时候只用svn update，不用再指定地址，所以第一次的checkout一般是要自己来做的，或者也可以指定地址让ant来帮你做)