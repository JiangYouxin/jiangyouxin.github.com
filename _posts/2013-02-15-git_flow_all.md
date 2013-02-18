---
layout: post
title: Git Flow, Why & How
---

{{ page.title }}
================

<p class="meta">2013.2.15, 北京</p>

如果你是个在厂里搞开发的，并且曾有过如下的遭遇：
(1) 你被要求马上发布版本，现实却是当前开发的某功能做了一半，现在做不完也毙不干净；
(2) 你开发的下一个版本的功能已经做完了，但你们车间的两个工友在做当前版本发布，于是你老没法提交代码，最后憋到内伤；
(3) 车间里正happy地开发新版本，突然厂里来了指示，要求在已发布版本基础上做一个小改动。
     结果你们痛苦地切分支改代码测试发版本，结果指示是执行了，这个改动却忘了合并到主线上。
(4) 各种其他……

那么，建议你试下Git Flow；当然，如果你们车间用的是SVN啥啥的，先看看Git吧。

以下介绍的是经过我“微创新”之后的Git Flow版本，修改了原版的一些BUG，某些细节上也有些差异。我做的改动具体可以看我前面的几篇博客。
{{ site.site_url }}/2013/02/12/git_flow_1.html
{{ site.site_url }}/2013/02/13/git_flow_2.html
{{ site.site_url }}/2013/02/14/git_flow_3.html

**获取代码：**

    git clone https://github.com/JiangYouxin/gitflow.git
    cd gitflow
    git checkout t/squash

**安装方法：**  
  (linux or cygwin) 直接: make install。
  (msysgit) 建议改用cygwin……

**简单使用说明: **

  首先你要用git做版本控制（好像是废话）。  
  去项目目录里执行 git flow init 可初始化git flow。这件事情你和你的每一个工友都要做。  
  剩下的事情见分割线下面的说明吧。

=======

**1. 开发新功能**

(1) 准备开发

   首先要为新开发的feature取一个名字<name>，使用命令：  

       git flow feature start <name>

   此时本地会基于develop分支（develop分支可以认为是开发主干，下面会简称主干），创建并切换到一个新的分支feature/<name>。
   
(2) 本地开发和提交

   专心研发指定的功能，不要做其它事情，包括（非该feature引入的）bug fix。
   有点像使用SVN时“将代码Hold在本地不提交”的状态。
   但git有本地代码库，可以做提交、回退，甚至再开分支。
    
   常用git命令：

       git add <file>：添加新文件（或老文件的修改）到暂存区
       git add -u：添加所有老文件的修改到暂存区
       git commit：将暂存区内容做本地提交
       git status：查看状态

   可以本地编版本，单独提测feature。
   当然，非该feature引入的BUG，要推迟到release分支打出来之后再修改。
   
(3) 多人协作

   同一个feature的开发需要多人协作时，首先把分支推到服务器上：

         git push origin feature/<name>

   其他人从服务器上把分支取下来：

         git fetch
         git checkout feature/<name>

   获取最新更新（类似SVN Update）：

         git pull origin feature/<name> --rebase
         # 如果出现冲突，需要手工解决并提交
 
   将本地已提交的修改推送到服务器（类似SVN Commit）：
     
         git push origin feature/<name>

(4) 完成开发

   先确认feature分支上所有修改都已经本地提交过了。
   如果有多人协作，还要确认大家都已经推到服务器，并且已经从获取了服务器的最新更新。
   如果feature单独提测，要等到基本无BUG后再做此操作。
 
   执行命令：

         git flow feature finish <name>

   这条命令会将feature分支上所有修改合并成一个，提交到主干。
   如果没有产生冲突，则会要求填写提交日志，这个日志会在主干上永久保存，要认真写。
   如果产生冲突，本地解决并提交后，重新执行上面的finish命令。
   成功后，feature分支会被删除，本地位于主干。

   之后更新并推送主干（即develop分支）：

         git pull origin develop --rebase
         git push origin develop

**2. 发布主干版本**

(1) 准备发布

   先把准备发布的feature都finish掉，保证本地主干是最新的。
   然后确定发布版本号<version>，它会作为版本发布时的TAG名称。执行命令：

         git flow release start <version>

   此时本地会基于主干，创建并切换到一个新的分支release/<version>。

   多人协作方式详见上面的 1 (3)，只是 feature/<name> 换成 release/<version>。

   release分支上主要是bug fix，小的需求变更等，不要做大feature。
   另外release分支上的每一个提交，日后都会在主干上永久保留，所以日志一定要认真写。
   
(2) 完成发布

   当release分支测试完成后，就可以发布版本了。
   确认release分支上所有修改都已经本地提交过了。
   且大家都已经推到服务器，并且已经从获取了服务器的最新更新。

   使用命令：

        git flow release finish <version>

   这条命令会在release分支当前位置打TAG，并把release分支合并到主干。
   如果产生冲突，本地解决并提交后，重新执行上面的finish命令。
   成功后，feature分支会被删除，本地位于主干。

   之后更新并推送主干（即develop分支），详见 1（4）。

   另外完成发布还会把本地master分支更新到指向最新版本，也要推送到服务器：
 
        git push origin master

**3. 发布紧急修复版本**

   与发布主干版本类似，参见上面的2。两点不同：
   
(1) 开始发布时要指定基准版本号。基于<base>做紧急修复的命令如下：
   
        git flow release start <version> <base>

(2) 如果<base>不是最新发布版（即与master不重合），结束发布时不会更新master。