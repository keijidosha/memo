# Tomcat Security

## Basic認証

$CATALINA_HOME/webapps/hoge/manage は admin と manager を許可する  
$CATALINA_HOME/conf/tomcat-users.xml  
```
<role rolename="hoge"/>
<user username="admin" password="password" roles="hoge"/>
<user username="manager" password="password" roles="hoge"/>
```

$CATALINA_HOME/webapps/hoge/WEB-INF/web.xml  
```
 <security-constraint>
 <web-resource-collection>
 <web-resource-name>hoge</web-resource-name>
 <url-pattern>/manage/*</url-pattern>
 </web-resource-collection>
 <auth-constraint>
 <role-name>hoge</role-name>
 </auth-constraint>
 </security-constraint>

 <login-config>
 <auth-method>BASIC</auth-method>
 <realm-name>Authentication for hoge</realm-name>    <== Basic認証ダイアログに表示される
 </login-config>

    <security-role>
        <role-name>hoge</role-name>
    </security-role>
```

## メソッド制限

$CATALINA_HOME/webapps/hoge/show は GET のみ  
$CATALINA_HOME/webapps/hoge/new は POST のみ許可する場合  
$CATALINA_HOME/webapps/hoge/WEB-INF/web.xml  
```
 <security-constraint>
 <web-resource-collection>
 <web-resource-name>show hoge</web-resource-name>
 <url-pattern>/show/*</url-pattern>
 <http-method>HEAD</http-method>
 <http-method>POST</http-method>
 <http-method>PUT</http-method>
 <http-method>PATCH</http-method>
 <http-method>DELETE</http-method>
 <http-method>OPTIONS</http-method>
 <http-method>TRACE</http-method>
 <http-method>LINK</http-method>
 <http-method>UNLINK</http-method>
 <http-method>CONNECT</http-method>
 <http-method>PROPFIND</http-method>
 <http-method>PROPPATCH</http-method>
 <http-method>MKCOL</http-method>
 <http-method>COPY</http-method>
 <http-method>MOVE</http-method>
 <http-method>LOCK</http-method>
 <http-method>UNLOCK</http-method>
 </web-resource-collection>
 <auth-constraint />
 </security-constraint>

 <security-constraint>
 <web-resource-collection>
 <web-resource-name>insert hoge</web-resource-name>
 <url-pattern>/insert/*</url-pattern>
 <http-method>HEAD</http-method>
 <http-method>GET</http-method>
 <http-method>PUT</http-method>
 <http-method>PATCH</http-method>
 <http-method>DELETE</http-method>
 <http-method>OPTIONS</http-method>
 <http-method>TRACE</http-method>
 <http-method>LINK</http-method>
 <http-method>UNLINK</http-method>
 <http-method>CONNECT</http-method>
 <http-method>PROPFIND</http-method>
 <http-method>PROPPATCH</http-method>
 <http-method>MKCOL</http-method>
 <http-method>COPY</http-method>
 <http-method>MOVE</http-method>
 <http-method>LOCK</http-method>
 <http-method>UNLOCK</http-method>
 </web-resource-collection>
 <auth-constraint />
 </security-constraint>
```  
禁止するメソッドを列挙する

## IPアドレス制限

$CATALINA_HOME/webapps/hoge にアクセスする IPアドレスを 192.168.1.0/24 と 127.0.0.1 に制限する場合  
$CATALINA_HOME/conf/Catalina/localhost/hoge.xml を作成  
```
<Context antiResourceLocking="false" privileged="true" >
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127.0.0.1|192.168.1.*" />
</Context>
```  
複数の IPアドレスを指定する場合、Tomcat 6以降は「|」で区切る。Tomcat 5 までは「,」

