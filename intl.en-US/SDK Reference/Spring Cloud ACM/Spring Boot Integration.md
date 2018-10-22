# Spring Boot Integration {#concept_ts4_c5w_42b .concept}

## Health check {#section_avt_c5w_42b .section}

Spring Cloud ACM incorporates the [Health Check](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html) of Spring Boot. Access the health endpoint to see if the Spring Boot application is properly connected to the ACM server:

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

Spring Cloud ACM also supports the [Refresh Scope](http://cloud.spring.io/spring-cloud-static/spring-cloud.html#_refresh_scope) feature of Spring Cloud.

The Bean marked with the annotation `@RefreshScope` automatically listens for the changes of the ACM server and updates the configuration at run time. Sample code:

```
@RestController
@RequestMapping("/sample")
@RefreshScope
class SampleController {

    @Value("${user.name}")
    String userName;
    
    @RequestMapping("/acm")
    public String simple() {
        return "Hello Spring Cloud ACM!" + " Hello " + userName + "!" ;
    }

}
```

The Bean marked with the annotation  `@ConfigurationProperties`  is subject to Refresh Scope by default.

## ACM Endpoint {#section_f4h_l5w_42b .section}

Spring Cloud ACM has a built-in endpoint named `acm`, which can be accessed by accessing /acm under the management.port \(8080 by default\) of Spring Boot applications, for example: [http://localhost:8080/acm](http://localhost:8080/acm)

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

## Considerations { .section}

-   When using Spring Boot 2.x, the ACM Endpoint path is `/actuator/acm`.
-   When using Spring Boot 2.x, you must add configuration `management.endpoints.web.exposure.include=*` before you can access ACM Endpoint.

