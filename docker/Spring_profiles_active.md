
<!--
##...create a new repository on the command line

__이것은 굵게입니다__ 또는 **이것은 굵게입니다** 
`main` 또는 `master` 코드나 명령어를 강조하려면 백틱(```)을 사용합니다.

방법 1: 줄 끝에 공백 두 개 추가 하면 줄 바뀜
2. 환경 변수 사용    
.env 파일 또는 Spring의 application.yml에서 환경에 맞는

-->

# 프로젝트에서 환경에 따라 설정을 변경
- application.yml 파일에서 로컬 환경과 서버(Docker를 포함한 배포 환경)에서 다른 설정을 사용하고 싶을 때, 
Spring Boot의 profiles 기능을 활용하면 구분할 수 있습니다. 
- Spring Boot는 환경별 설정을 쉽게 관리할 수 있도록 `application-{profile}.yml` 형식을 지원합니다.
  - 예를 들어, `application-local.yml`은 로컬 환경, `application-prod.yml`은 배포 환경에서 사용되도록 설정할 수 있습니다.

## 1. 프로파일 설정 방법
1) 환경별 설정 파일 이름 
   - 로컬 (Local): application-local.yml
   - 개발 (Development): application-dev.yml
   - 운영 (Production): application-prod.yml
2) 프로파일 설정:
   - 각 환경에 맞는 application-<[profile]>.yml 파일을 작성합니다.
   - 각 파일에 해당 환경에 필요한 설정을 정의합니다.
3) Spring Boot 애플리케이션 실행 시 프로파일 지정 
   - Docker 또는 다른 배포 방법을 사용할 때, 환경 변수 SPRING_PROFILES_ACTIVE를 설정합니다.
   - 예를 들어:
     - 로컬: SPRING_PROFILES_ACTIVE=local
     - 개발: SPRING_PROFILES_ACTIVE=dev
     - 운영: SPRING_PROFILES_ACTIVE=prod
```yaml
version: '3'
services:
  app:
    image: slee-rentHome:latest
    environment:
      - SPRING_PROFILES_ACTIVE=local  # 또는 dev, local
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/renthome_prod  # 환경에 맞게 수정
      - SPRING_DATASOURCE_USERNAME=prod_user  # 환경에 맞게 수정
      - SPRING_DATASOURCE_PASSWORD=prod_password  # 환경에 맞게 수정
```


## 2. 프로파일 설정 적용
### 1. 프로파일 설정
- 기본 환경: application.yml
- 로컬 환경: application-local.yml
- 배포 환경(Docker 포함): application-prod.yml

1) `application.yml` (기본 설정)
   - 이 파일은 공통 설정을 담아두는 용도로 사용됩니다.
   ```yaml
   spring:
      application:
         name: slee-rentHome
      profiles:
         active: local  # 기본적으로 로컬 환경을 사용
   ```
 
2) `application-local.yml` (로컬 환경 설정)
   - 로컬에서 서버를 올릴 때 사용할 설정을 여기에 작성합니다.
   ```yaml
   spring:
      datasource:
         url: jdbc:postgresql://localhost:5432/renthome_local
         username: local_user
         password: local_password
         driverClassName: org.postgresql.Driver
      jpa:
         hibernate:
            ddl-auto: update
         show-sql: true
   ```


3) `application-prod.yml` (배포 환경 설정)
   - Docker 등 서버에서 사용할 설정은 여기에 작성합니다. Docker 환경에서는 환경 변수로 설정 값을 주입하는 것이 일반적입니다.
   ```yaml
   spring:
      datasource:
         url: ${SPRING_DATASOURCE_URL}
         username: ${SPRING_DATASOURCE_USERNAME}
         password: ${SPRING_DATASOURCE_PASSWORD}
         driverClassName: org.postgresql.Driver
      jpa:
         hibernate:
            ddl-auto: update
         show-sql: false  # 배포 환경에서는 로그 출력 최소화
   ```

4) Docker에서 환경 변수 주입
   - docker-compose.yml 파일에서 환경 변수를 설정하여 배포 환경에 맞는 설정을 주입할 수 있습니다.
   ```yaml
   version: '3'
   services:
     app:
       image: slee-rentHome:latest
       environment:
         - SPRING_PROFILES_ACTIVE=prod   # 또는 dev, local
         - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/renthome_prod  # 환경에 맞게 수정
         - SPRING_DATASOURCE_USERNAME=prod_user  # 환경에 맞게 수정
         - SPRING_DATASOURCE_PASSWORD=prod_password  # 환경에 맞게 수정
   ```

5) 로컬에서 서버 실행 시
   - 로컬에서는 명시적으로 프로파일을 지정하지 않으면 application-local.yml이 기본적으로 사용되도록 설정되어 있으므로 별도로 설정할 필요가 없습니다. 
   - 하지만 특정 프로파일을 지정하고 싶다면 다음과 같이 실행할 수 있습니다.
   ```bash
   # 로컬 프로파일로 실행
   ./mvnw spring-boot:run -Dspring-boot.run.profiles=local
   ````

6) IntelliJ 나 이클립스 실행시
   - VM Options에 프로파일 설정
   ```bash
   -Dspring.profiles.active=local
   ````
   - Program Arguments에 프로파일 설정
   ```bash
   --spring.profiles.active=local
   ````
7) IntelliJ Run/Debug Configurations
   - Build and run 섹션의 Active profiles 항목에 local이라고 입력하면 됩니다.
     - IntelliJ의 상단 오른쪽에서 Run/Debug Configurations 메뉴를 엽니다.
     - 실행하고자 하는 Application 설정을 선택합니다.
     - Build and run 섹션 아래에 있는 Active profiles 항목에 local을 입력합니다.