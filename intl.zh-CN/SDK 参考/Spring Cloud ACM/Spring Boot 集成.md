# Spring Boot 集成 {#concept_ts4_c5w_42b .concept}

## 健康检查 {#section_avt_c5w_42b .section}

Spring Cloud ACM 集成了 Spring Boot 的 [Health Check](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html)。访问 health endpoint 可以看到 Spring Boot 应用是否正确连接了 ACM 服务器：

```
{
  "status": "UP",
  "acm": {
    "status": "UP",
    "dataIds": [
      "com.alibaba.cloud.acm:sample-app.properties"
    ]
  },
  "diskSpace": {
    "status": "UP",
    "total": 1000240963584,
    "free": 858827423744,
    "threshold": 10485760
  },
  "refreshScope": {
    "status": "UP"
  }
}
```

## @RefreshScope {#section_tpd_f5w_42b .section}

Spring Cloud ACM 也支持 Spring Cloud 的 [Refresh Scope](http://cloud.spring.io/spring-cloud-static/spring-cloud.html#_refresh_scope) 特性。

标记了 `@RefreshScope` 注解的 Bean 会自动监听 ACM 服务端的变更，并在运行时自动更新配置。代码示例如下：

```
@RestController
@RequestMapping("/sample")
@RefreshScope
class SampleController {
    @Value("${user.name}")
    String userName;
    @RequestMapping("/acm")
    public String simple() {
        return "Hello Spring Cloud ACM!" + " Hello " + userName + "!";
    }
}
```

使用了 `@ConfigurationProperties` 注解的 Bean 默认就是 Refresh Scope 的。

## ACM Endpoint {#section_f4h_l5w_42b .section}

Spring Cloud ACM 内置了一个名为 `acm` 的 endpoint，您可以通过访问 Spring Boot 应用 management.port （默认为 8080）下的 /acm 访问，如：[http://localhost:8080/acm](http://localhost:8080/acm)

```
{
  "runtime": {
    "sources": [
      {
        "dataId": "com.alibaba.cloud.acm:sample-app.properties",
        "lastSynced": "2017-10-10 10:46:27"
      }
    ],
    "refreshHistory": [
      {
        "timestamp": "2017-10-10 10:46:24",
        "dataId": "com.alibaba.cloud.acm:sample-app.properties",
        "md5": "8692ae986ec7bc345b3f0f4de602ff13"
      }
    ]
  },
  "config": {
    "group": "DEFAULT_GROUP",
    "timeOut": 3000,
    "endpoit": "xxx",
    "namespace": "xxx",
    "accessKey": "xxx",
    "secretKey": "xxx"
  }
}
```

## 备注 { .section}

-   使用 Spring Boot 2.x 时，ACM Endpoint 访问路径为 `/actuator/acm`。
-   使用 Spring Boot 2.x 时，必须添加配置 `management.endpoints.web.exposure.include=*` 才能访问 ACM Endpoint。

