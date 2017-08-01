# cas
简单的cas客户端配置说明以及需要自己部署的cas服务器。<br>

在做这个之前本来只是想给我的web项目添加个访问权限，同时又不喜欢简单创建数据库需求的用户表，于是打算通过cas实现单点登录。<br>

*文件的上传主要分2个部分。<br>
  *第一部分是需要访问项目需要pom.xml和web.xml文件的配置。
  *第二部分则是上传我使用的cas服务器，因为是用的是官方默认的cas服务器，所以登录页面可能有点丑（有需求可以自己改）。

  在web.xml上配置了cas拦截器，只要输入符合拦截器网址，页面会先跳转到cas服务器，在cas服务器成功登录后，页面就可以直接访问了。这里需要对tomcat、mavaen
拦截器有个简单的了解。至于cas服务器的默认帐号密码请在cas文件夹中找到deployerConfigContext.xml里查看帐号密码，也可进行简单的修改我这边上传的帐号密码
默认是fujian bonc123。<br>

因为我这边可能不方便直接上传整个web.xml文件和pom.xml配置。我把需要配置的地方直接写在下面。<br>

在pom.xml文件下引人下面jar包
		<!-- https://mvnrepository.com/artifact/org.jasig.cas.client/cas-client-core -->
		<dependency>
			<groupId>org.jasig.cas.client</groupId>
			<artifactId>cas-client-core</artifactId>
			<version>3.2.1</version>
		</dependency>
	</dependencies>
  
在web.xml文件下添加下面的cas拦截器

      <filter>
        <filter-name>ssoSingOutFilter</filter-name>
        <filter-class>org.jasig.cas.client.session.SingleSignOutFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>ssoSingOutFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
	
	<listener>
        <listener-class>org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>
    </listener>

    <filter>
        <filter-name>ssoAuthFilter</filter-name>
        <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
        <init-param>
            <param-name>casServerLoginUrl</param-name>
            <param-value> 
                http://localhost:9000/cas/login  //这里填写你的cas服务器地址登录
            </param-value>
        </init-param>
        <init-param>
            <param-name>service</param-name>
            <param-value>
                http://localhost:8088/resource-manage  //这里填写你布置cas客户端默认进入的地址
            </param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>ssoAuthFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <filter>
        <filter-name>ssoValidateFilter</filter-name>
        <filter-class>org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter
        </filter-class>
        <init-param>
            <param-name>casServerUrlPrefix</param-name>
            <param-value>
                http://localhost:9000/cas   
            </param-value>
        </init-param>
        <init-param>
            <param-name>service</param-name>
            <param-value> 
                http://localhost:8088/resource-manage
            </param-value>
        </init-param>
        <init-param>  
            <param-name>encoding</param-name>  
            <param-value>UTF-8</param-value>  
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>ssoValidateFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
