---
id: 32
title: Silverlight debugging made simple(r)
date: 2013-09-03T17:42:01+00:00
author: Etienne
layout: post
guid: http://geeksperiment.com/?p=32
permalink: /silverlight-debugging-made-simpler/
image: /wp-content/uploads/2013/09/silverlight2-640x290.png
categories:
  - Tips and tricks
tags:
  - debug
  - event viewer
  - silverlight
---
Eventhough Silverlight has been declared dead by rumors, it is still a powerful framework to work with. An example of what you can do with it is: <https://www.wiki-os.org/>, which contains great dev tools and snippets to begin with&#8230;and maybe a little too much transition effects. 🙂<!--more-->

Anyways, I recently ran into the issue of troubleshooting/debugging an application deployed on a server with a completely different configuration than my dev environment and with no possibility to install all the necessary resources to easily debug. Since the server is on a different domain as my personal computer, the <a title="Remote Debugger" href="http://msdn.microsoft.com/en-us/library/vstudio/y7f5zaaa.aspx" target="_blank">Remote Debugger</a> option was quite painful.

So I decided to deploy a version that logs a whole more stuff without adding notifications on the  GUI. I used two methods depdending on the scope (&laquo;&nbsp;where I am&nbsp;&raquo; in the application when I log stuff) :

  * Logging things in your browser console:
  * Adding entries in the event viewer:

&nbsp;

# In the browser console

This method will only work if you want to log stuff known in the code behind of your silverlight page.

Create a new empty class, I called it ConsoleLog, add the System.Windows.Browser librairy (you can find it the following Silverlight directory: _C:Program Files (x86)Microsoft Silverlight5.1.20513.0System.Windows.Browser.dll_) and then create a public method:

```csharp
using System;

using System.Windows.Browser;

namespace Dalkia.Dispatch.Business.GoogleSync

{

    class ConsoleLog

    {

        public static void WriteLog(this object obj)

        {

            HtmlWindow window = HtmlPage.Window;

            var isConsoleAvailable = (bool)window.Eval("typeof(console)" +

                    " != &#039;undefined&#039; &amp;&amp; typeof(console.log) != &#039;undefined&#039;");

            if (isConsoleAvailable)

            {

                var console = window.Eval("console.log") as ScriptObject;

                if (console != null)

                {

                    console.InvokeSelf(obj);

                }

            }

        }

    }

}
```
# In the eventviewer

If you want to log stuff from elsewhere than the code behind of a Silverlight page, you&rsquo;ll have to specify a particular log, your error/message and the source:

```csharp
using System;

using System.Diagnostics;

namespace Jeudis

{

    class EventViewerLogger

    {

        public static void WriteLog(string entrySource, string entryLog, EventLogEntryType entryType, string entryMessage)

        {

            if (!EventLog.SourceExists(entrySource))

                EventLog.CreateEventSource(entrySource, entryLog);

//entrylog: Possible values include Application, System, or a custom event log

            //http://msdn.microsoft.com/fr-fr/library/system.diagnostics.eventlog.writeentry.aspx

            EventLog.WriteEntry(entrySource, entryMessage, entryType);

        }

    }

}
```
# But&#8230;

What I&rsquo;ve shown isn&rsquo;t&#8230;let&rsquo;s say &laquo;&nbsp;optimal&nbsp;&raquo; and in the end I knew that I&rsquo;ll have to delete all this &laquo;&nbsp;nasty&nbsp;&raquo; code. 🙂

Eventhough these two methods can come in handy (if you&rsquo;re in a hurry), they should not be used for production version for two reasons. The first one is that they are not suitable/adapted for large complexe applications and the second is that you can easily find good free libraries that helps you put up your own logging system for your application. And that&rsquo;s what should be looking for if you want to have let&rsquo;s say a clean directory with one or more logging files in which you can easily log and access.