# 환경별 환경 변수 또는 설정파일 사용

axios의 baseURL을 로컬 환경과 서버 환경에서 다르게 설정할 수 있습니다.

이를 위해 환경 변수 또는 환경별 설정 파일을 사용하는 방법을 적용할 수 있습니다.

환경에 따라 다른 URL을 설정하는 가장 일반적인 방법은 환경 변수를 사용하는 것입니다.

<br>

[Vite 환경 변수 적용](ProcessEnv_Vite.md)  
Vite 프로젝트에서도 환경 변수를 활용하여 axios의 baseURL을 로컬과 서버 환경에서 다르게 설정할 수 있습니다
<br>


### 1 환경 변수 사용 (JavaScript의 process.env)
.env 파일을 사용해 환경 변수를 정의할 수 있습니다.
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

4. 코드에서 환경 변수 사용   
.env 파일을 설정하면 React, Vue, Next.js 같은 프레임워크에서는 process.env로 해당 값을 접근할 수 있습니다.

```javascript
export const api = axios.create({
   baseURL: process.env.REACT_APP_API_BASE_URL || "http://localhost:9192"
});
````
- 로컬에서 실행 시 http://localhost:9192로 연결되고,
- 프로덕션에서는 http://your_domain_address:9192로 연결됩니다.



### 2 Build Scripts 사용
package.json의 scripts 섹션에서 환경 변수를 설정하고 이를 process.env.NODE_ENV로 구분할 수도 있습니다.

1. package.json에 스크립트 추가

```json
{
   "scripts": {
      "start": "react-scripts start",
      "start:local": "cross-env REACT_APP_API_BASE_URL=http://localhost:9192 react-scripts start",
      "build": "react-scripts build",
      "build:prod": "cross-env REACT_APP_API_BASE_URL=http://your_domain_address:9192 react-scripts build"
   }
}
````
- cross-env는 플랫폼에 상관없이 환경 변수를 설정하는 도구입니다. 설치하려면 다음 명령어를 사용합니다.

   ```bash
   npm install cross-env --save-dev
   ````

2. 코드에서 process.env 사용
```javascript
export const api = axios.create({
   baseURL: process.env.REACT_APP_API_BASE_URL || "http://localhost:9192"
});
````
- npm run start:local로 실행하면 로컬 환경의 URL이 적용됩니다. 
- npm run build:prod로 빌드하면 프로덕션 URL이 적용됩니다.


### 3. 별도의 설정 파일을 통한 관리

환경별로 다른 설정 파일을 만들어 관리하는 방법입니다. 

1. 설정 파일
- config.local.js
   ```javascript
   export const API_BASE_URL = "http://localhost:9192";
   ````
- config.prod.js
   ```javascript
   export const API_BASE_URL = "http://your_domain_address:9192";
   ````

2. 코드 적용

```javascript
const config = process.env.NODE_ENV === 'production'
        ? require('./config.prod')
        : require('./config.local');

export const api = axios.create({
   baseURL: config.API_BASE_URL
});
````