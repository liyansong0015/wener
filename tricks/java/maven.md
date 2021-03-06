将 jar 安装到本地仓库
----
mvn install:install-file -Dfile=c:\kaptcha-{version}.jar -DgroupId=com.google.code -DartifactId=kaptcha -Dversion={version} -Dpackaging=jar

mvn install:install-file
-Dfile=<path-to-file>
-DgroupId=<group-id>
-DartifactId=<artifact-id>
-Dversion=<version>
-Dpackaging=<packaging>
-DgeneratePom=true

Where: <path-to-file>  the path to the file to load
   <group-id>      the group that the file should be registered under
   <artifact-id>   the artifact name for the file
   <version>       the version of the file
   <packaging>     the packaging of the file e.g. jar

打包 jar 的参数   
----
http://maven.apache.org/plugins/maven-jar-plugin/jar-mojo.html


准备好上传
---------
:: 手册 https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide
cd ..
mvn source:jar javadoc:jar
cd target
cp ../pom.xml .
SET PROJ_NAME=IKAnalyzer-2012_u6
del *.asc
gpg -ab pom.xml
gpg -ab %PROJ_NAME%.jar
gpg -ab %PROJ_NAME%-sources.jar
gpg -ab %PROJ_NAME%-javadoc.jar

直接部署存在的
-------------
:: 具体参数参考 http://maven.apache.org/plugins/maven-gpg-plugin/sign-mojo.html
:: 可以直接在命令行上指定密码
PASSPHRASE=xxx
mvn gpg:sign-and-deploy-file -Dgpg.passphrase=%PASSPHRASE% ^
	-Durl=https://oss.sonatype.org/service/local/staging/deploy/maven2/ -DrepositoryId=sonatype-nexus-staging -DpomFile=pom.xml -Dfile=IKAnalyzer-2012_u6.jar
mvn gpg:sign-and-deploy-file -Dgpg.passphrase=%PASSPHRASE% ^
	-Durl=https://oss.sonatype.org/service/local/staging/deploy/maven2/ -DrepositoryId=sonatype-nexus-staging -DpomFile=pom.xml -Dfile=IKAnalyzer-2012_u6-sources.jar -Dclassifier=sources
mvn gpg:sign-and-deploy-file -Dgpg.passphrase=%PASSPHRASE% ^
	-Durl=https://oss.sonatype.org/service/local/staging/deploy/maven2/ -DrepositoryId=sonatype-nexus-staging -DpomFile=pom.xml -Dfile=IKAnalyzer-2012_u6-javadoc.jar -Dclassifier=javadoc

上传时的 gpg认证
----------------
https://github.com/sevntu-checkstyle/dsm-maven-plugin/wiki/How-to-config-GPG-and-sign-artifact-with-it

gpg --gen-key
gpg --list-keys
gpg --keyserver hkp://pool.sks-keyservers.net --send-keys 09CB6FEF
gpg -ab artifact.jar
gpg --verify artifact.jar.asc

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-gpg-plugin</artifactId>
	<version>1.4</version>
	<executions>
		<execution>
			<id>sign-artifacts</id>
			<phase>verify</phase>
			<goals>
				<goal>sign</goal>
			</goals>
		</execution>
	</executions>
</plugin>

# maven 使用 socks 代理
export MAVEN_OPTS="-DsocksProxyHost=192.168.1.103 -DsocksProxyPort=8087"
export MAVEN_OPTS="-DhttpProxyHost=192.168.1.103 -DhttpProxyPort=8087 -DhttpsProxyHost=192.168.1.103 -DhttpsProxyPort=8087"

# 不调用javadoc 插件
-Dmaven.javadoc.skip=true
# 不测试
-Dmaven.test.skip=true
# -Dmaven.test.skip prevents Maven building the test-jar artifact.
# If you'd like to skip tests but create artifacts as per a normal build use:
-Dmaven.test.skip.exec

# maven 代理设置
<proxy>
	<id>myproxy</id>
	<active>true</active>
	<protocol>http</protocol>
	<host>127.0.0.1</host>
	<port>8087</port>
	<nonProxyHosts>localhost|127.0.0.1</nonProxyHosts>
</proxy>

# spring 的仓库
http://repo.spring.io/libs-milestone
https://code.lds.org/nexus/content/groups/main-repo
https://repository.jboss.org/nexus/content/repositories/releases
http://repo.typesafe.com/typesafereadonly/releases
# 添加其他的仓库
<repository>
	<id>nexus-osc</id>
	<url>http://maven.oschina.net/content/groups/public/</url>
</repository>
# maven 配置 conf/settings.xml
# 国内镜像
<mirror>  
		<id>nexus-osc</id>  
      <mirrorOf>*</mirrorOf>  
	  <name>Nexusosc</name>  
	  <url>http://maven.oschina.net/content/groups/public/</url>  
</mirror> 

<profile>
	<id>jdk-1.7</id>  
	<activation>  
		<jdk>1.7</jdk>  
	</activation>  
	<repositories>  
		<repository>  
			<id>nexus</id>  
			<name>local private nexus</name>  
			<url>http://maven.oschina.net/content/groups/public/</url>  
			<releases>  
				<enabled>true</enabled>  
			</releases>  
			<snapshots>  
				<enabled>false</enabled>  
			</snapshots>  
		</repository>  
	</repositories>  
	<pluginRepositories>  
		<pluginRepository>  
			<id>nexus</id>  
			<name>local private nexus</name>  
			<url>http://maven.oschina.net/content/groups/public/</url>  
			<releases>  
				<enabled>true</enabled>  
			</releases>  
			<snapshots>  
				<enabled>false</enabled>  
			</snapshots>  
		</pluginRepository>  
	</pluginRepositories>  
</profile>