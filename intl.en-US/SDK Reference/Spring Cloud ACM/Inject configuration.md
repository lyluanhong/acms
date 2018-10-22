# Inject configuration {#concept_vcr_vtw_42b .concept}

Use the Spring MVC annotation to inject the configurations and reduce configuration management costs.

The  `@Value` can be used directly to inject the configurations:

```
@Component
class SampleRunner implements ApplicationRunner {
    
    @Value("${user.id}")
    String userId;

    @Value("${user.name}")
    String userName;
    
    @Value("${user.age}")
    int userAge;
    
    @Override
    public void run(ApplicationArguments args){
        System.out.println(userId);
        System.out.println(userName);
        System.out.println(userAge);
    }

}
```

**Note:** If the same key is configured in `application.properties` of the Spring Boot application and  `${spring.application.group}:${spring.application.name}.properties` of ACM at the same time, the value in ACM overrides the default value of the application.

