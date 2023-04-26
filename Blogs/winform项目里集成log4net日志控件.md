## 引入log4net.dll文件
使用NuGet安装或者直接引入对应dll

## 为项目添加 log4net.config 文件
格式如下：
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
	<configSections>
		<section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler,log4net" />
	</configSections>
	<log4net>
		<root>
		</root>
		<!--Error信息使用ErrorAppender组件来配置-->
		<logger name="Error">
			<level value="ALL" />
			<appender-ref ref="ErrorAppender" />
		</logger>
		<!--Info信息使用InfoAppender组件来配置-->
		<logger name="InfoLog">
			<level value="ALL" />
			<appender-ref ref="InfoAppender" />
		</logger>
		
		<appender name="ErrorAppender" type="log4net.Appender.RollingFileAppender,log4net" >			
			<param name="File" type="" value="Log/Error/" />			
			<param name="AppendToFile" value="true" />						
			<param name="RollingStyle" value="Date" />
			<param name="DatePattern" value="yyyyMMdd\\yyyyMMdd-HHmmss" />
			
			<param name="StaticLogFileName" value="false" />
			<layout type="log4net.Layout.PatternLayout,log4net">
				<param name="ConversionPattern" value="%n[%d] %c %n%m%n%n" />
				<param name="Header" value="-----------BEGIN-----------" />
				<param name="Footer" value="------------END------------" />
			</layout>
		</appender>

		<!--Info信息组件-->
		<appender name="InfoAppender" type="log4net.Appender.RollingFileAppender,log4net" >
			<!--文件路径，如果RollingStyle为Composite或Date，则这里设置为目录，文件名在DatePattern里设置，其他则这里要有文件名。已经扩展支持虚拟目录-->		
			<!--StaticLogFileName为true，这里设置指定文件名不再支持yyyyMMdd这种匹配日期的格式-->
			<param name="File" type="" value="Log/Info/mylog.txt" />
			<!--是否在文件中追加，默认为true, 设置为false会将之前的记录覆盖-->
			<param name="AppendToFile" value="true" />
			<!--最多产生的日志文件数，超过则只保留最新的n个。设定值value="－1"为不限文件数-->
			<!--<param name="MaxSizeRollBackups" value="-1" />-->
			<!--当RollingStyle为Composite或Size，这里设置最大文件大小（可以KB，MB，GB为单位，默认为字节）-->
			<!--<param name="MaxFileSize" value="300MB" />-->

			<!--创建新文件的方式，可选为Size（按文件大小），Date（按日期），Once（每启动一次创建一个文件），Composite（按日期及文件大小），默认为Composite-->
			<!--设置为Date则每隔一段时间会生成一个日志文件,上面MaxFileSize对其不生效；设置为Size则会根据MaxFileSize的设置自动生成新文件-->			
			<!--参考该文：Log4Net MaxSizeRollBackups和MaxFileSize不生效问题解决办法 https://www.cnblogs.com/Khan-Sadas/p/12802687.html-->
			<!--<param name="RollingStyle" value="Date" />-->
			<!--当RollingStyle为Composite或Date，这里设置文件名格式-->
			<!--<param name="DatePattern" value="yyyyMMdd\\yyyyMMdd-HHmmss" />-->
			<!--是否使用静态文件名 true/false，默认为true。为true时，RollingStyle的值将无效。且为true时，需要在File里指定文件名，所有日志都会记录在这个文件里。-->
			<param name="StaticLogFileName" value="true" />
			<layout type="log4net.Layout.PatternLayout,log4net">
				<param name="ConversionPattern" value="%n[%d] %c %n%m%n%n" />
				<param name="Header" value="-----------BEGIN-----------" />
				<param name="Footer" value="------------END------------" />
			</layout>
		</appender>
	</log4net>
</configuration>
```
然后在该config文件上右键，点击属性，设置``复制到输出目录``这一项为``始终复制``

## 在Properties下的AssemblyInfo.cs中添加一行
```c
[assembly: log4net.Config.XmlConfigurator(ConfigFile = "log4net.config", Watch = true)]
```
## log4netHelper类的实现
```csharp
public class log4netHelper
    {
        /// <summary>
        /// 普通日志
        /// </summary>
        /// <param name="message">日志内容</param>
        public static void Info(string message)
        {
            log4net.ILog log = log4net.LogManager.GetLogger("InfoLog");
            if (log.IsInfoEnabled)
            {
                log.Info(message);
            }
            log = null;
        }
        /// <summary>
        /// 错误日志
        /// </summary>
        /// <param name="message">错误日志</param>
        public static void Error(string message)
        {
            log4net.ILog log = log4net.LogManager.GetLogger("Error");
            if (log.IsInfoEnabled)
            {
                log.Error(message);
            }
            log = null;
        }
    }
```

## 使用
```c
log4netHelper.Info("这是一条普通日志");
log4netHelper.Error("这是一条错误日志");
```

在项目的bin下对应目录会生成日志文件，普通日志只生成一个文件，错误日志会过一段时间就生成一个。
## 其它
- 上述提到的config文件可以通过配置设置日志文件的生产规则，笔者测试只保留有用的配置，过多参数配置的话会导致无法生成日志文件。

- 日志分级配置：https://blog.51cto.com/cplvfx/5392100