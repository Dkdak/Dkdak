# CORS(Cross-Origin Resource Sharing)

웹 브라우저에서 실행되는 보안 기능.

한 출처(origin)에서 로드된 웹 페이지가 다른 출처의 리소스에 접근할 수 있는지를 제어합니다. 

- 기본적으로, 웹 브라우저는 보안상의 이유로 같은 출처 정책(Same-Origin Policy)을 따릅니다. 
- 이 정책에 따르면, 한 출처의 웹 페이지가 다른 출처의 리소스에 접근하는 것이 제한됩니다.


<br>

## CORS의 주요 개념

1. 출처(Origin): URL의 프로토콜, 호스트, 포트가 합쳐진 것을 의미합니다. 
   - 예를 들어, https://example.com:3000은 https, example.com, 3000의 조합입니다.

2. Preflight 요청: CORS에서는 특정 HTTP 요청이 실제 요청을 보내기 전에 OPTIONS 메서드를 사용하여 서버에 허가를 요청합니다.
   - 이 요청을 "preflight" 요청이라고 합니다.
3. CORS 헤더: 서버는 응답에 CORS 관련 헤더를 포함시켜야 합니다. 
   - 가장 일반적인 헤더는 Access-Control-Allow-Origin으로, 이 헤더에 접근을 허용할 출처를 지정합니다.


## 예시
1. 허용된 요청: 만약 서버가 Access-Control-Allow-Origin: 
   - `*` 로 설정되어 있다면, 모든 출처에서의 요청을 허용합니다.
2. 특정 출처 허용: Access-Control-Allow-Origin: 
   - https://example.com로 설정하면, 해당 출처에서의 요청만 허용됩니다.

<br>


## 환경에 맞는 도메인만 허용하도록 적용

CORS 설정에서 모든 도메인을 허용하려면 addAllowedOrigin("*")을 사용하여 모든 출처를 허용할 수 있습니다. 

또한, 헤더와 메서드에 대한 제한도 없애려면 각각 addAllowedHeader("*")와 
setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE", "OPTIONS")) 등을 설정하면 됩니다.

그러나, 모든 출처를 허용하는 것은 보안적인 리스크가 있으므로, 가능한 특정 도메인에 대해서만 허용하는 것이 좋습니다

개발 환경과 프로덕션 환경에서 다른 도메인을 사용해야 한다면, 환경 변수나 프로파일을 사용하여 동적으로 변경할 수 있습니다.


### 1. 모든 출처, 헤더, 메서드를 허용하는 CORS 설정

모든 출처와 헤더, 메서드를 허용하려면 아래와 같이 설정할 수 있습니다:

```java
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class WebConfig {

   private static final long MAX_AGE = 3600;

   @Bean
   public CorsFilter corsFilter() {
      CorsConfiguration config = new CorsConfiguration();
      config.setAllowCredentials(true);

      // 모든 출처를 허용
      // 모든 출처 허용 (특히 Spring Boot 2.4 이후에는 addAllowedOrigin 대신 사용)
      config.addAllowedOriginPattern("*");  

      // 모든 헤더 허용
      config.addAllowedHeader("*");

      // 모든 HTTP 메서드 허용
      config.setAllowedMethods(Arrays.asList(
              HttpMethod.GET.name(),
              HttpMethod.POST.name(),
              HttpMethod.PUT.name(),
              HttpMethod.DELETE.name(),
              HttpMethod.OPTIONS.name()));  // OPTIONS 메서드도 허용

      config.setMaxAge(MAX_AGE);

      UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
      source.registerCorsConfiguration("/**", config);

      return new CorsFilter(source);
   }
}

```
<br>

### 2. CORS 설정 동적으로 변경 (환경에 따라)  
프로덕션과 로컬 환경에서 출처를 다르게 설정하고 싶다면, Spring의 @Value나 @Profile을 사용해 환경 변수를 읽어들여 설정할 수 있습니다.

1. 의존성 추가 
   - @Value 애너테이션을 사용하기 위해서는 Spring Framework의 `spring-context` 모듈이 필요합니다.
   ```gradle
   dependencies {
       implementation 'org.springframework:spring-context'
       implementation 'org.springframework:spring-web'
   }
   ```
   - Spring Boot 프로젝트에서는 기본적으로 포함되어 있기 때문에 별도로 추가할 필요는 없습니다.
   하지만 Gradle을 사용하고 있다면, 아래와 같이 `spring-boot-starter`를 의존성에 추가해야 합니다.
   ```gradle
   dependencies {
       implementation 'org.springframework.boot:spring-boot-starter'
       implementation 'org.springframework.boot:spring-boot-starter-web'
   }
   ```

2. 환경 변수 사용  
`.env` 파일 또는 Spring의 `application.yml`에서 환경에 맞는 값을 설정하고, 이를 코드에서 동적으로 적용하는 방법입니다.
   - application.yml (로컬 환경):
   ```yaml
   cors:
     allowed-origin: "http://localhost:5173"
   ``` 
   - application-prod.yml (프로덕션 환경):
   ```yaml
   cors:
     allowed-origin: "http://yourdomain_address:5173"
   ``` 

3. 환경 변수 적용  
@Value를 통해 application.yml에 설정된 값을 읽어 CORS 설정을 동적으로 변경할 수 있습니다.

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class WebConfig {

   private static final long MAX_AGE = 3600;

   @Value("${cors.allowed-origin}")
   private String allowedOrigin;

   @Bean
   public CorsFilter corsFilter() {
      CorsConfiguration config = new CorsConfiguration();
      config.setAllowCredentials(true);

      // 환경 변수로 출처 설정
      config.addAllowedOrigin(allowedOrigin);

      // 모든 헤더 허용
      config.addAllowedHeader("*");

      // 모든 HTTP 메서드 허용
      config.setAllowedMethods(Arrays.asList(
              HttpMethod.GET.name(),
              HttpMethod.POST.name(),
              HttpMethod.PUT.name(),
              HttpMethod.DELETE.name(),
              HttpMethod.OPTIONS.name()));

      config.setMaxAge(MAX_AGE);

      UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
      source.registerCorsConfiguration("/**", config);

      return new CorsFilter(source);
   }
}

```
