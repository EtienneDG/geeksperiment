---
id: 584
title: Effortless xwiki running on Windows Server
date: 2014-07-09T15:15:16+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=584
permalink: /effortless-xwiki-running-on-windows-server/
categories:
  - Uncategorized
format: aside
---
# Effortless xwiki running on Windows Server

[Xwiki](http://enterprise.xwiki.org/xwiki/bin/view/Main/Download) is one of the numerous platforms with which you can store knowledge in a structured way. Easily customizable if you&rsquo;re comfortable with Java (Velocity and other Java derivated languages). It is open source and&#8230;created by cool french people. 😀

Installing it is quite easy, keeping it running can get tricky:

  * Need to [download apache daemons](http://www.apache.org/dist/commons/daemon/binaries/windows/commons-daemon-1.0.15-bin-windows.zip)
  * Unzip and rename the two .exe files as you want (srv=Service ; mgr = Manager). For instance XWikiJettyService.exe (same with Manager)
  * Install the service by running the following command (&laquo;&nbsp;D:\XWiki Entreprise 5.4.3\&nbsp;&raquo; is my xwiki installation folder, just replace it with your own). You might need to create the log folder before running the command though.

```
XWikiJettyService.exe  //IS//XWikiJettyServiceManager --DisplayName=&#034;XWiki Jetty Service&#034; --Install=&#034;D:\XWiki Entreprise 5.4.3\XWikiJettyService.exe&#034; --LogPath=&#034;D:\XWiki Entreprise 5.4.3\jetty\logs&#034; --LogLevel=Debug --StdOutput=auto --StdError=auto --StartMode=jvm --StopMode=jvm --Jvm=auto --Startup=auto --JvmMx=512 --StartPath=&#034;D:\XWiki Entreprise 5.4.3&#034; ++JvmOptions=-XX:MaxPermSize=128m --Classpath=&#034;D:\XWiki Entreprise 5.4.3\jetty\start.jar&#034; --StartClass=org.eclipse.jetty.start.Main --StopClass=org.eclipse.jetty.stop.Main ++StopParams=--stop ++JvmOptions=-Djetty.home=&#034;D:\XWiki Entreprise 5.4.3\jetty&#034; ++JvmOptions=-Djetty.port=8080 ++JvmOptions=-DSTOP.PORT=8079 ++JvmOptions=-DSTOP.KEY=xwiki ++JvmOptions=-Djetty.logs=&#034;D:\XWiki Entreprise 5.4.3\jetty\logs&#034; ++JvmOptions=-Dfile.encoding=UTF8 ++JvmOptions=-Dorg.mortbay.jetty.Request.maxFormContentSize=1000000
```

&nbsp;

  * The service should be running (check it out in the Service manager)
  * BONUS: create a task that (re)boot the xwiki periodically. A simple .bat in the /jetty folder (where the start.jar is located) with this line :

```
java -jar start.jar
```

&nbsp;

[Sources](https://delphinus.qns.net/xwiki/bin/view/Blog/Windows+Service+for+Jetty+7)