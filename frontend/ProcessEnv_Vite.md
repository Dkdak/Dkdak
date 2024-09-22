# Vite는 환경 변수 파일 사용


### 1 환경 변수 사용 (JavaScript의 process.env)
Vite에서는 .env 파일을 사용해 환경 변수를 설정할 수 있으며, 접두사로 VITE_를 사용해야 합니다.


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

2. .env 파일 설정
  - 로컬 개발 환경 (.env.development):   
    ```bash
    VITE_API_BASE_URL=http://localhost:9192
    ````
  
  - 프로덕션 환경 (.env.production):
    ```bash
    VITE_API_BASE_URL=http://your_domain_address:9192
    ````

3. 코드에서 환경 변수 사용   
   axios를 설정할 때 import.meta.env를 사용해 환경 변수를 읽어올 수 있습니다.
```javascript
export const api = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL || "http://localhost:9192"
});
````
  - 로컬에서 실행 시 http://localhost:9192로 연결되고,
  - 프로덕션에서는 http://your_domain_address:9192로 연결됩니다.

4. Vite 실행 스크립트  
   package.json에서 Vite 빌드와 실행 스크립트를 추가하여 환경별로 실행할 수 있습니다.
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "serve": "vite preview"
  }
}
```
- npm run dev: 로컬 개발 환경에서 실행 (.env.development 사용)
- npm run build: 프로덕션 환경에서 빌드 (.env.production 사용)`



### 2 vite.config.js 를 사용하는 방법
vite.config.js 파일은 Vite의 빌드 설정을 커스터마이징하는 데 사용되며, 환경 변수를 직접 설정하거나 다룰 수 있습니다.
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
2. vite.config.js에서 환경 변수를 활용하는 방법  
   Vite의 vite.config.js 파일에서 import.meta.env로 접근할 수 있는 환경 변수를 사용할 수 있습니다.

   - vite.config.js 설정
```javascript
import { defineConfig } from 'vite';

export default defineConfig(({ mode }) => {
  // mode 값에 따라 다른 환경 변수를 로드할 수 있음
  const isProduction = mode === 'production';

  return {
    define: {
      'process.env.VITE_API_BASE_URL': JSON.stringify(process.env.VITE_API_BASE_URL),
    },
    server: {
      proxy: {
        // 예시: 개발 환경에서 API 요청을 프록시할 수 있음
        '/api': {
          target: isProduction
            ? 'http://your_domain_address:9192' // 프로덕션 API URL
            : 'http://localhost:9192',     // 로컬 개발 API URL
          changeOrigin: true,
          rewrite: (path) => path.replace(/^\/api/, ''),
        }
      }
    }
  };
});
````



3. .env 파일 설정
- 로컬 개발 환경 (.env.development):
  ```bash
  VITE_API_BASE_URL=http://localhost:9192
  ````

- 프로덕션 환경 (.env.production):
  ```bash
  VITE_API_BASE_URL=http://your_domain_address:9192
  ````

4. 코드에서 환경 변수 사용   
   axios를 설정할 때 import.meta.env를 사용해 환경 변수를 읽어올 수 있습니다.
```javascript
export const api = axios.create({
    baseURL: import.meta.env.VITE_API_BASE_URL || "http://localhost:9192"
});
````
- 로컬에서 실행 시 http://localhost:9192로 연결되고,
- 프로덕션에서는 http://your_domain_address:9192로 연결됩니다.

5. Vite 실행 스크립트  
   package.json에서 Vite 빌드와 실행 스크립트를 추가하여 환경별로 실행할 수 있습니다.
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "serve": "vite preview"
  }
}
```
- npm run dev: 로컬 개발 환경에서 실행 (.env.development 사용)
- npm run build: 프로덕션 환경에서 빌드 (.env.production 사용)`    