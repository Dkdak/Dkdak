# 환경별 환경 변수 또는 설정파일 사용

axios의 baseURL을 로컬 환경과 서버 환경에서 다르게 설정할 수 있습니다.

이를 위해 환경 변수 또는 환경별 설정 파일을 사용하는 방법을 적용할 수 있습니다.

환경에 따라 다른 URL을 설정하는 가장 일반적인 방법은 환경 변수를 사용하는 것입니다.


<br>

### 1 환경 변수 사용 (JavaScript의 process.env)
1. 프로젝트 구조:
   ```arduino
   ├── node_modules
   ├── public
   ├── src
   ├── .env.development
   ├── .env.production
   ├── package.json
   ├── vite.config.js
   
   ```
2. 로컬 개발 환경 (.env.development):   
```bash
VITE_API_BASE_URL=http://localhost:9192

````
3. 프로덕션 환경 (.env.production):

```bash
VITE_API_BASE_URL=http://your_domain_address:9192
````


### 2 Build Scripts 사용


### 3. 별도의 설정 파일을 통한 관리

