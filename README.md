jfinal-dreampie
===============

dreampie.cn 支持我请在右上角点 Star，thanks


jfinal好用的插件正在开发，

下载源码:https://github.com/Dreampie/jfinal-dreampie,
maven引入方式：http://search.maven.org/ 输入jfinal-dreampie搜索:
```xml
<dependencies>
   <dependency>
      <groupId>cn.dreampie</groupId>
      <artifactId>jfinal-dreampie</artifactId>
      <version>1.1</version>
    </dependency>
</dependencies>
```
demo:(Angularjs前端+jfinal-dreampie插件):https://github.com/Dreampie/icedog,
剥离框架前的源码:https://github.com/Dreampie/dreampie

https://github.com/Dreampie?tab=repositories 目录下有多款插件，可以在maven里搜索到：

cn.dreampie.jfinal-shiro     https://github.com/Dreampie/jfinal-shiro    shiro插件

cn.dreampie.jfinal-shiro-freemarker   https://github.com/Dreampie/jfinal-shiro-freemarker    shiro插件实现的freemarker标签库

cn.dreampie.jfinal-web     https://github.com/Dreampie/jfinal-web   相关web插件，简洁model实现

cn.dreampie.jfinal-utils        https://github.com/Dreampie/jfinal-utils   部分jfinal工具

cn.dreampie.jfinal-tablebind        https://github.com/Dreampie/jfinal-tablebind   jfinal的table自动绑定插件，支持多数据源

cn.dreampie.jfinal-flyway      https://github.com/Dreampie/jfinal-flyway   数据库脚本升级插件，开发中升级应用时，使用脚本同步升级数据库或者回滚

cn.dreampie.jfinal-captcha      https://github.com/Dreampie/jfinal-captcha   基于jfinal render的超简单验证码插件

cn.dreampie.jfinal-quartz       https://github.com/Dreampie/jfinal-quartz   基于jfinal 的quartz管理器

cn.dreampie.jfinal-sqlinxml      https://github.com/Dreampie/jfinal-sqlinxml   基于jfinal 的类似ibatis的sql语句管理方案

cn.dreampie.jfinal-lesscss       https://github.com/Dreampie/jfinal-lesscss   java实现的lesscsss实时编译插件，可以由于jfinal

cn.dreampie.jfinal-coffeescript     https://github.com/Dreampie/jfinal-coffeescript   java实现的coffeescript实时编译插件，可以由于jfinal

cn.dreampie.jfinal-akka    https://github.com/Dreampie/jfinal-akka   java使用akka执行异步任务

cn.dreampie.jfinal-mailer       https://github.com/Dreampie/jfinal-mailer   使用akka发布邮件的jfinal插件

cn.dreampie.jfinal-slf4j     https://github.com/Dreampie/jfinal-slf4j   让jfinal使用slf4j的日志api

部分代码正在调试和开发中，欢迎大家使用

介绍->


jfinal-dreampie是一个基于jfinal的开源框架库，主要集成或调优功能如下（由于篇幅原因部分源码未贴出，可以到github下载源码）:

1.缓存维护更细粒度
```java
@CacheNameRemove(name = AppConstants.DEFAULT_CACHENAME)


@Inherited
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface CacheNameRemove {
  String name();

  String[] keys() default {};
}
//根据key值来移除基于某个特定方法的缓存，而不是移除整个controller下的缓存

String controllerKey = ai.getControllerKey();
    if (!ValidateUtils.me().isNullOrEmpty(removeCacheName)) {
      String keyPrefix = null;
      if (!ValidateUtils.me().isNullOrEmpty(removeCacheKeys)) {
        for (String removeKey : removeCacheKeys) {
          keyPrefix = controllerKey + SLASH + removeKey;
          removeByCacheKey(removeCacheName, keyPrefix);
        }
      } else {
        keyPrefix = controllerKey;
        removeByCacheKey(removeCacheName, keyPrefix);
      }
    }
```

2.增加Slf4jLogger实现，然日志配置能基于任何Slf4j下的log实现，如logback

```java
public class Slf4jLogFactory implements ILoggerFactory{
}
```
3.基础model实现，实现基础的常用的部分功能用于复用

``` java
 public List<M> findAll() {
    return find(getSelectSql() + getExceptSelectSql());
  }

  public List<M> findBy(String where, Object... paras) {
    return find(getSelectSql() + getExceptSelectSql() + getWhere(where), paras);
  }

  public List<M> findTopBy(int topNumber, String where, Object... paras) {
    return paginate(1, topNumber, getSelectSql(), getExceptSelectSql() + getWhere(where), paras).getList();
  }

  public M findFirstBy(String where, Object... paras) {
    return findFirst(getSelectSql() + getExceptSelectSql() + getWhere(where), paras);
  }

  public Page<M> paginateAll(int pageNumber, int pageSize) {
    return paginate(pageNumber, pageSize, getSelectSql(), getExceptSelectSql());
  }

  public Page<M> paginateBy(int pageNumber, int pageSize, String where, Object... paras) {
    return paginate(pageNumber, pageSize, getSelectSql(), getExceptSelectSql() + getWhere(where), paras);
  }

  public boolean updateAll(String set, Object... paras) {
    return Db.update(getUpdateSql() + getSet(set), paras) > 0;
  }

  public boolean updateBy(String set, String where, Object... paras) {
    return Db.update(getUpdateSql() + getSet(set) + getWhere(where), paras) > 0;
  }

  public boolean deleteAll() {
    return Db.update(getDeleteSql()) > 0;
  }

  public boolean deleteBy(String where, Object... paras) {
    return Db.update(getDeleteSql() + getWhere(where), paras) > 0;
  }

  public boolean dropAll() {
    return Db.update(getDropSql()) > 0;
  }

  public boolean dropBy(String where, Object... paras) {
    return Db.update(getDropSql() + getWhere(where), paras) > 0;
  }
```
4.akka异步执行框架
```java
public class AkkaPlugin implements IPlugin


Akka.system().scheduler().scheduleOnce(Duration.create(1000, TimeUnit.MILLISECONDS),
        new Runnable() {
          @Override
          public void run() {
            //do Something
          }
        }, Akka.system().dispatcher());
```
5.atmosphere  chat demo
``` java
@ManagedService(path = "/im/{room: [a-zA-Z][a-zA-Z_0-9]*}")
public class ChatRoom {
}
```
6.coffeescript  compiler plugin,编译coffeescript代码，并监控文件改动重新编译
``` java
public class CoffeeScriptPlugin implements IPlugin {
}
```
7.Flyway 数据库自动脚本升级 Plugin，用于自动生成或升级数据库，避免不断使用数据库工具修改数据导致的版本问题
``` java
public class FlywayPlugin implements IPlugin {
}
```
8.lesscss compiler plugin，编译less代码，并监控文件改动重新编译
``` java
public class LessCssPlugin implements IPlugin {
}
```
9.mailer 邮件发送plugin，使用akka异步发送邮件
``` java
 Akka.system().scheduler().scheduleOnce(Duration.create(1000, TimeUnit.MILLISECONDS),
        new Runnable() {
          @Override
          public void run() {
            MailerConf mailerConf = MailerPlugin.mailerConf;
            HtmlEmail htmlEmail = new HtmlEmail();
            htmlEmail.setCharset(mailerConf.getCharset());
            htmlEmail.setSocketTimeout(mailerConf.getTimeout());
            htmlEmail.setCharset(mailerConf.getEncode());
            htmlEmail.setHostName(mailerConf.getHost());
            if (!ValidateUtils.me().isNullOrEmpty(mailerConf.getSslport()))
              htmlEmail.setSslSmtpPort(mailerConf.getSslport());
            if (!ValidateUtils.me().isNullOrEmpty(mailerConf.getPort()))
              htmlEmail.setSmtpPort(Integer.parseInt(mailerConf.getPort()));
            htmlEmail.setSSLOnConnect(mailerConf.isSsl());
            htmlEmail.setStartTLSEnabled(mailerConf.isTls());
            htmlEmail.setDebug(mailerConf.isDebug());
            htmlEmail.setAuthenticator(new DefaultAuthenticator(mailerConf.getUser(), mailerConf.getPassword()));
            try {
              htmlEmail.setFrom(mailerConf.getFrom(), mailerConf.getName());
              htmlEmail.setSubject(subject);
              htmlEmail.addTo(recipients);
              htmlEmail.setHtmlMsg(body);
              // set the alternative message
              htmlEmail.setTextMsg("Your email client does not support HTML messages");
              if (!ValidateUtils.me().isNullOrEmpty(attachment))
                htmlEmail.attach(attachment);
              htmlEmail.send();
            } catch (EmailException e) {
              e.printStackTrace();
            }
          }
        }, Akka.system().dispatcher());
```
10.自定义验证码，可以自定义使用字符或者数字，颜色和样式可以自调

``` java
public class PatchcaRender extends Render {
  private static final String CODE_CHAR = "0123456789";
}
```
11.QuartzPlugin使用quartz执行定时任务，参照部分网络实例，简易实现

``` java
QuartzFactory.me().startJobOnce(TimeUtils.me().toString(DateTime.now()), ++jobId, "stati",this.getClass().getSimpleName(), OrderDataJob.class, param);
```
12.shiro权限框架，基于数据库url配置过滤，验证码验证
``` java

//读取数据库权限只需实现该接口
public interface JdbcAuthzService {
  public Map<String, AuthzHandler> getJdbcAuthz();
}
```

13.shiro的freemarker标签库

``` java
<@shiro.hasPermission name="P_USER">
   <li><a href="/admin/user">${i18n.getText("admin.user")}</a></li>
</@shiro.hasPermission>
```
14.xss过滤的StringEscapeUtils过滤器实现
``` java
public class AttackHandler extends Handler {
  @Override
  public void handle(String target, HttpServletRequest request, HttpServletResponse response, boolean[] isHandled) {
    request = new HttpServletRequestWrapper(request);
    nextHandler.handle(target, request, response, isHandled);
  }
}
```
15.json数据请求时，返回的error信息使用json字符串
``` java
public class JsonErrorRenderFactory implements IErrorRenderFactory {
  public Render getRender(int errorCode, String view) {
    if (ThreadLocalUtil.isJson())
      return new JsonErrorRender(errorCode, view);
    else
      return new ErrorRender(errorCode, view);
  }
}
```

16.自动绑定多数据源的tablebind
``` java
AutoMultiSourceTableBindPlugin tableBindDefault = new AutoMultiSourceTableBindPlugin(druidDefault, SimpleNameStyles.LOWER);
//让default数据源排除shop目录下的model，因为该目录的model属于custom数据源
tableBindDefault.addExcludePaths("cn.dreampie.function.shop");

 AutoMultiSourceTableBindPlugin tableBindCustom = new AutoMultiSourceTableBindPlugin(druidCustom, SimpleNameStyles.LOWER);
//让custom只扫描shop目录，注意当使用includepaths是只会扫描配置的路径，如果没有使用includepaths会扫描全路径
tableBindCustom.addIncludePaths("cn.dreampie.function.shop");
```
17.http压缩请求数据的gzipFilter，可测试
```xml
<!--gzip compress filter-->
<filter>
  <filter-name>gzipFilter</filter-name>
  <filter-class>cn.dreampie.common.web.filter.gzip.GZIPFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>gzipFilter</filter-name>
  <url-pattern>/*</url-pattern>
  <dispatcher>REQUEST</dispatcher>
  <dispatcher>FORWARD</dispatcher>
  <dispatcher>INCLUDE</dispatcher>
  <dispatcher>ERROR</dispatcher>
</filter-mapping>
<!--gzip compress filter-->
```
18.http缓存cacheFilter，和gzipFilter共同用于提高用户性能
```xml
<!-- cache filter-->
  <filter>
    <filter-name>cacheFilter</filter-name>
    <filter-class>cn.dreampie.common.web.filter.cache.CacheFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>cacheFilter</filter-name>
    <url-pattern>/*</url-pattern>
    <dispatcher>REQUEST</dispatcher>
    <dispatcher>FORWARD</dispatcher>
    <dispatcher>INCLUDE</dispatcher>
    <dispatcher>ERROR</dispatcher>
  </filter-mapping>
  <!--cache filter-->
```
如有问题请及时联系我 在线访问：http://www.dreampie.cn
