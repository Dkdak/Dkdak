
<!--
**Dkdak/Dkdak** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->


# 안녕하세요! 👋

저는 Dkdak 입니다. 주로 **Java**를 기반으로 한 **MSA**을 사용하는 소프트웨어 개발자입니다. 현재는 **클라우드 기반 백엔드 개발**과 **데이터 과학** 분야에 관심을 두고 있습니다.


***

## 보유중인 기술 스택

### 언어
![Java](https://img.shields.io/badge/Java-007396?style=for-the-badge&logo=java&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)

### 프레임워크 및 라이브러리
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)
![Spring Framework](https://img.shields.io/badge/Spring%20Framework-6DB33F?style=for-the-badge&logo=spring&logoColor=white)
![JPA](https://img.shields.io/badge/JPA-59666C?style=for-the-badge&logo=hibernate&logoColor=white)

### 데이터베이스
![OracleDB](https://img.shields.io/badge/OracleDB-F80000?style=for-the-badge&logo=oracle&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)

### 도구 및 플랫폼
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)
![Jira](https://img.shields.io/badge/Jira-0052CC?style=for-the-badge&logo=jira&logoColor=white)
![Kafka](https://img.shields.io/badge/Kafka-231F20?style=for-the-badge&logo=apachekafka&logoColor=white)

### 기타
![MSA](https://img.shields.io/badge/MSA-008FCC?style=for-the-badge)
![RestAPI](https://img.shields.io/badge/RestAPI-008FC7?style=for-the-badge)
![MES](https://img.shields.io/badge/MES-33A7FF?style=for-the-badge)
![SCM](https://img.shields.io/badge/SCM-8E44AD?style=for-the-badge)


***

# 프로젝트 문서

## 가이드 문서

### 프로젝트 상세 설정 가이드
- docker
  - [React+Vite Frontend](docker%2FReact_Frond.md)
    - Vite를 빌드 도구로 사용하는 React 프로젝트를 Docker와 통합합니다.  
  - [Spring Boot Backend ](docker%2FSpringBoot_PG.md)
    - Docker와 Docker Compose를 사용하여 Gradle Spring Boot 프로젝트와 PostgreSQL을 설정합니다. 
  - [FrondEnd && Backend](https://github.com/Dkdak/docker_project)
    - Docker와 Docker Compose 설치하고, frond와 backend(postgresql포함)에 대한 전체 설정정보를 제공합니다.
  - [Redis Container](docker%2FRedis_Container.md)
    - Docker로 Redis 서버를 쉽게 실행하고 관리할 수 있습니다.


- FrondEnd
  - [React 및 Vite 설정](frontend%2FReact_Vite.md)
    - React + Vite 에 대한 설정을 제공합니다.
  - [빌드 환경별 설정](frontend%2FProcessEnv.md)
    - axios의 baseURL을 로컬 환경과 서버 환경에서 다르게 설정할 수 있습니다.
    - [Vite에서의 빌드 환경별 설정](frontend%2FProcessEnv_Vite.md)은 import.meta.env를 사용해 환경 변수를 사용합니다. 



- Backend
  - [profiles 환경 설정](docker%2FSpring_profiles_active.md)
    - 프로젝트에서 환경에 따라 설정을 변경하는 방법으로는 주로 프로파일을 사용하는 것이 좋습니다.
  - [CORS(Cross-Origin Resource Sharing)](backend%2FCorsFilter.md)
    - 출처(origin)에서 로드된 웹 페이지가 다른 출처의 리소스에 접근할 수 있는지를 제어합니다. 


- MSA
  - 인메모리를 활용한 file 관리 전략 (Redis편)
    - [Redis_FileCache 전략](MSA%2FRedis_FileCache.md) : Spring Boot와 Redis 캐시, CLOB 타입 DB의 파일 관리
  - [Spring Data JPA에서 제공하는 쿼리 메서드(Query Method)](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)
    - 쿼리 메서드의 사용법, 이름 규칙, 다양한 쿼리 생성 방법 등을 자세히 설명하고 있습니다.
  - [Spring Data JPA Projections](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html#projections)
    - 인터페이스 기반 프로젝션, DTO(데이터 전송 객체) 프로젝션, 그리고 프로젝션 사용 예시 등을 설명하고 있습니다.

## 학습중인 기술
[![React](https://img.shields.io/badge/React-61DAFB?style=for-the-badge&logo=react&logoColor=black)](https://reactjs.org)
[![CI/CD](https://img.shields.io/badge/CI%2FCD-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)](https://github.com/features/actions)
[![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)](https://aws.amazon.com)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://docs.docker.com)


***

# 프로젝트

## 진행중인 프로젝트
![Project Badge](https://img.shields.io/badge/Project%20Name-room--rent-blue?style=for-the-badge)
<br>설명: 이 프로젝트는 호텔 예약을 위한 react 튜토리얼 버전입니다.

- 프론트엔드: [React 클라이언트](https://github.com/dkdak/rentRoom-client)
    - ![Node.js](https://img.shields.io/badge/Node.js-%E2%9C%94%20v20.14.0-green)
    - ![React](https://img.shields.io/badge/React-%E2%9C%94%20v18.2.0-blue)
    - ![Vite](https://img.shields.io/badge/Vite-%E2%9C%94%20v4.2.0-blue)
      - **Vite**: 최신 웹 개발 도구로, 빠른 개발 서버 시작, 즉각적인 모듈 핫 리로딩, 그리고 최적화된 프로덕션 빌드를 제공합니다. [자세히 알아보기](https://vitejs.dev).

- 백엔드: [Spring Boot 백엔드](https://github.com/dkdak/rentRoom-server)
    - ![Gradle](https://img.shields.io/badge/Gradle-%E2%9C%94%20v8.2-blue)
    - ![OpenJDK](https://img.shields.io/badge/OpenJDK-%E2%9C%94%20v17-blue)
    - ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-%E2%9C%94%20v15.0-blue)
    - ![JPA](https://img.shields.io/badge/JPA-%E2%9C%94-blue)
    - ![Hibernate](https://img.shields.io/badge/Hibernate-%E2%9C%94%20v6.0.0-yellowgreen)
        - Hibernate는 JPA를 구현한 프레임워크로, 캐싱, lazy/eager loading, 복잡한 쿼리, 다중 데이터베이스 지원 등 고급 기능을 제공합니다.
    - ![Spring Boot](https://img.shields.io/badge/Spring%20Boot-%E2%9C%94%20v3.0.0-green)
    - ![Spring Security](https://img.shields.io/badge/Spring%20Security-%E2%9C%94%20v6.0.0-orange)
    - ![JWT](https://img.shields.io/badge/JWT-%E2%9C%94%20v0.11.5-lightgrey)
        - JSON Web Token (JWT)은 인증 및 권한 부여를 위한 compact, URL-safe token을 제공합니다.




  
### [Project 2](https://github.com/yourusername/project2)
![Project 2](https://img.shields.io/badge/Project%202-Python%2C%20Django-blue?style=for-the-badge)
- 설명: 이 프로젝트는...


## GitHub 통계
![Your GitHub stats](https://github-readme-stats.vercel.app/api?username=yourusername&show_icons=true)
![Top Langs](https://github-readme-stats.vercel.app/api/top-langs/?username=yourusername&layout=compact)


<!--
## 연락처
![Email](https://img.shields.io/badge/your.email@example.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/yourprofile)
-->
