---
layout: default
title: Maven Jetty 插件启动多个webapp
---

# {{ page.title }}
## 需求
有时候我们开发的webapp需要与其他webapp集成在一个jvm中运行，虽然可以把自己的webapp打包后部署到应用服务器，但是开发阶段直接使用Maven的Jetty插件会更方便。

## 解决方案
修改pom.xml文件如下即可（重点是contextHandlers节点）
```xml
<plugin>
	<groupId>org.eclipse.jetty</groupId>
	<artifactId>jetty-maven-plugin</artifactId>
	<version>9.4.6.v20170531</version>
	<configuration>
		<stopPort>9999</stopPort>
		<stopKey>stopit</stopKey>
		<webApp>
			<contextPath>/dmse</contextPath>
		</webApp>
		<contextHandlers>
			<contextHandler implementation="org.eclipse.jetty.maven.plugin.JettyWebAppContext">
				<war>D:\webapps\assetls</war>
				<contextPath>/assetls</contextPath>
			</contextHandler>
		</contextHandlers>
	</configuration>
</plugin>
```

方法来源于[stackoverflow](https://stackoverflow.com/questions/5519066/possible-to-run-two-webapps-at-once-when-developing-with-maven-eclipse) ，另外还有一个更复杂的方法，我没有测试。
