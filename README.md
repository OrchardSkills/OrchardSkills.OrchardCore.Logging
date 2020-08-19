# Adding Logging to an Orchard Core CMS Web Application

## Debugging with a Log File

# [YouTube Video](https://youtu.be/DVYDitHBHik)

[![OrchardSkillsYouTubeThumbNailLog](https://user-images.githubusercontent.com/59172485/90595003-110cde00-e1a9-11ea-9ccf-88a8502674f4.png)](https://youtu.be/DVYDitHBHik)

# Introduction

One of the key aspects required for troubleshooting an application is creating a quality logging system. We all know the need for logging the code we create. Logging is essential to understanding the behaviour of an application and to debug unexpected issues or for simply tracking events. In the production environment, we can’t debug issues without proper log files as they become the only source of information to debug some intermittent or unexpected errors.

![Orchard-Core-Logging-001](https://user-images.githubusercontent.com/59172485/90594942-f9355a00-e1a8-11ea-9b84-6d711a935e71.png)

Launch Visual Studio and load the MyOrchardCoreCMS solution. You can get the source [here](https://github.com/OrchardSkills/OrchardSkills.OrchardCore.Logging). Right click on "Manage NuGet Packages..." in the project dependencies.




![Orchard-Core-Logging-002](https://user-images.githubusercontent.com/59172485/90594944-f9cdf080-e1a8-11ea-9127-412dd05adbc2.png)

Select the "Browse" tab. In the search box search for "OrchardCore.Logging.NLog", make sure the "Include prerelease" is checked, and press the "Install" button.



![Orchard-Core-Logging-003](https://user-images.githubusercontent.com/59172485/90594946-fa668700-e1a8-11ea-9a8e-c5f493ec2780.png)

Press the "OK" button to accept changes.



![Orchard-Core-Logging-004](https://user-images.githubusercontent.com/59172485/90594949-faff1d80-e1a8-11ea-8417-9ff727f79af8.png)

Add a new Item to the project.



![Orchard-Core-Logging-005](https://user-images.githubusercontent.com/59172485/90594951-fb97b400-e1a8-11ea-94c8-a0d161e932ef.png)

Select a "Test File".



![Orchard-Core-Logging-006](https://user-images.githubusercontent.com/59172485/90594955-fcc8e100-e1a8-11ea-8906-270537ec5d03.png)

Rename it to "NLog.config".



![Orchard-Core-Logging-007](https://user-images.githubusercontent.com/59172485/90594957-fcc8e100-e1a8-11ea-81f8-9ed4649ea54b.png)

Add the following to the config file:

```
<?xml version="1.0" encoding="utf-8" ?>
<!--
Level Typical Use
Fatal Something bad happened; application is going down
Error Something failed; application may or may not continue
Warn  Something unexpected; application will continue
Info  Normal behavior like mail sent; user updated profile etc.
Debug For debugging; execute query; user authenticated, session expired
Trace For trace debugging; begin method X, end method X
-->
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      autoReload="true"
      internalLogLevel="Warn"
      internalLogFile="App_Data/logs/internal-nlog.txt">

	<extensions>
		<add assembly="NLog.Web.AspNetCore"/>
		<add assembly="OrchardCore.Logging.NLog"/>
	</extensions>

	<targets>
		<!-- file target -->
		<target xsi:type="File" name="file"
				fileName="${var:configDir}/App_Data/logs/orchard-log-${shortdate}.log"
				layout="${longdate}|${orchard-tenant-name}|${aspnet-traceidentifier}|${event-properties:item=EventId.Id}|${logger}|${uppercase:${level}}|${message} ${exception:format=ToString,StackTrace}"
        />

		<!-- console target -->
		<target xsi:type="Console" name="console" />

	</targets>

	<rules>
		<!-- all warnings and above go to the file target -->
		<logger name="*" minlevel="Warn" writeTo="file" />

		<!-- the hosting lifetime events go to the console -->
		<logger name="Microsoft.Hosting.Lifetime" minlevel="Info" writeTo="console" />
	</rules>
</nlog>

```



![Orchard-Core-Logging-008](https://user-images.githubusercontent.com/59172485/90594958-fdfa0e00-e1a8-11ea-9372-dbb2c7cad775.png)

Modify the "Programs.cs" file to:

```
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using OrchardCore.Logging;

namespace MyOrchardCoreCMS.Web
{
    public class Program
    {
        public static Task Main(string[] args)
            => BuildHost(args).RunAsync();

        public static IHost BuildHost(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureLogging(logging => logging.ClearProviders())
                .ConfigureWebHostDefaults(webBuilder => webBuilder
                    .UseStartup<Startup>()
                    .UseNLogWeb()
                ).Build();
    }
}

```



![Orchard-Core-Logging-009](https://user-images.githubusercontent.com/59172485/90594960-fe92a480-e1a8-11ea-8dee-f4bfa5b8f7d0.png)

Modify the "appsettings.json" file to:

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}

```



![Orchard-Core-Logging-010](https://user-images.githubusercontent.com/59172485/90594962-fe92a480-e1a8-11ea-97df-6cb7deff6e3a.png)

Here are the logging levels described.



![Orchard-Core-Logging-011](https://user-images.githubusercontent.com/59172485/90594966-ff2b3b00-e1a8-11ea-82b4-5a1c6b2112fe.png)

Here is the location of the log file. 

```
MyOrchardCoreCMSLogging\App_Data\Logs\internal-nlog.txt
```





![Orchard-Core-Logging-012](https://user-images.githubusercontent.com/59172485/90594967-ffc3d180-e1a8-11ea-8a71-cb256b079f9a.png)

Log listing



# Conculsion

We modified the MyOrchardCoreCMS Web Application, created on a previous video and added Orchard Core logging. We went through the coding and configuration settings, Went over all the the different logging levels (Fatal, Error, Warn, Info, Debug and Trace). Then we ran the application, After exiting the application. We opened the detailied log and followed it's logging events. Logging in any application is very important, you can log a lot of critical things and gain unprecedented insight into how your application is working and performing. It is a language/framework agnostic practice which I recommend you to start from today if you are not already doing it.

# GitHub

The complete source code is located [here](https://github.com/OrchardSkills/OrchardSkills.OrchardCore.Logging).
