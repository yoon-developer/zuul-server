Spring Cloud Netflix Zuul (Server)
==========

# 1. build.gradle
> 버전  
- spring cloud: Hoxton.SR10  
- spring-cloud-starter-netflix-zuul: 2.2.7
- spring-cloud-starter-netflix-eureka-client: 2.2.7

> dependencies 추가
- org.springframework.cloud:spring-cloud-starter-netflix-zuul
- org.springframework.cloud:spring-cloud-starter-netflix-eureka-client

```text
ext {
	set('springCloudVersion', "Hoxton.SR10")
}

dependencies {
	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
	implementation 'org.springframework.cloud:spring-cloud-starter-netflix-zuul'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}
```

# 2. Application.yml

> 서버 포트 설정
```yaml
server:
  port: 80
```

> eureka 설정
- spring.applicaion.name: eureka 서비스 등록시 서비스 이름 설정
- eureka.client.serviceUrl.defaultZone: DefaultZone Url 설정을 통해 동일한 zone의 eureka server clustering 설정
- eureka.instance.instance-id: 서비스 ID 설정

```yaml
spring:
  application:
    name: zuul-server

eureka:
  instance:
    instance-id: ${spring.application.name}:${spring.application.instance_id:${random.value}}
  client:
    serviceUrl:
      defaultZone: ${EUREKA_URL:http://127.0.0.1:8761/eureka/}
```

> zuul 설정
- zuul.routes.service-1.path: 해당 path 로 요청 올 경우  
  - **: 모든 path 
- zuul.routes.service-1.serviceId: 해당 path 요청을 serviceId 로 전달

```yaml
zuul:
  routes:
    service-1:
      path: /**
      serviceId: eureka-client
```

# 3. Code

> @EnableZuulProxy, @EnableDiscoveryClient 추가
```java
@EnableZuulProxy
@EnableDiscoveryClient
@SpringBootApplication
public class ZuulServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ZuulServerApplication.class, args);
	}

}
```