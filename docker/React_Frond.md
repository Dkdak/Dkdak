# Docker Frontend Setup
이 문서는 Vite를 빌드 도구로 사용하는 React 프로젝트를 Docker와 통합하는 설정 단계를 제공합니다.

## 1. 기본설정

1) 프로젝트 구조
   프로젝트 기본 구조를 아래와 같습니다.
```css
my-project
├── frontend
│   ├── Dockerfile
│   ├── package.json
│   └── src
└── docker-compose.yml
```

2) 프로젝트 클론
```bash
cd C:/my-project/frontend
git clone https://github.com/yourusername/your-repo.git .
```
<br>

## 2. React + Vite를 위한 Dockerfile
1) Vite 프로젝트 디렉토리의 루트에 `Dockerfile`을 생성합니다:

```Dockerfile
# Node.js 이미지를 사용
FROM node:20.14.0

# 작업 디렉토리를 /app으로 설정
WORKDIR /app

# package.json 및 package-lock.json 파일을 작업 디렉토리로 복사
COPY package*.json ./

# 의존성 설치
RUN npm install

# 프로젝트의 모든 파일을 작업 디렉토리로 복사
COPY docker .

# React 앱의 기본 포트는 3000, 하지만 사용자가 지정한 포트를 사용
EXPOSE 5173

# 앱 실행 명령어 ( npm run dev를 실행하여 Vite 개발 서버를 시작)
CMD ["npm", "run", "dev"]

```

2) 프로덕션 환경용 Dockerfile
   프로덕션 환경에서는 Vite의 빌드된 정적 파일을 제공할 수 있는 서버가 필요합니다. 다음은 Vite로 빌드한 애플리케이션을 Nginx로 제공하는 Dockerfile입니다:

```Dockerfile
# 단계 1: 빌드 단계
FROM node:20.14.0 AS build

# 작업 디렉토리 설정
WORKDIR /app

# package.json 및 package-lock.json 복사
COPY package*.json ./

# 의존성 설치
RUN npm install

# 프로젝트 파일 복사
COPY .. .

# Vite 빌드
RUN npm run build

# 단계 2: 실행 단계
FROM nginx:alpine

# 빌드 단계에서 생성된 정적 파일을 Nginx로 복사
COPY --from=build /app/dist /usr/share/nginx/html

# Nginx의 기본 포트 열기
EXPOSE 80

# Nginx 실행
CMD ["nginx", "-g", "daemon off;"]

```
- 1) 빌드 단계:
     Node.js 이미지를 사용하여 의존성을 설치하고 npm run build로 Vite 애플리케이션을 빌드합니다. 빌드된 결과는 /app/dist 디렉토리에 저장됩니다.
- 2) 실행 단계:
     Nginx 이미지를 사용하여 정적 파일을 제공하는 서버를 설정합니다. /app/dist에서 빌드된 파일을 Nginx의 /usr/share/nginx/html로 복사합니다.
     Nginx를 실행하여 포트 80에서 HTTP 요청을 수신합니다.


## 3. docker-compose 설정
docker-compose.yml 파일을 다음과 같이 작성합니다.
```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "5173:5173"  # 개발 환경에서 Vite 서버 포트 매핑
    volumes:
      - ./frontend/src:/app/src  # 개발 중 소스 코드의 핫 리로딩을 위해 마운트
    environment:
      - NODE_ENV=development
    restart: unless-stopped

```


### 4. 오류 해결
1) Docker에 정상적으로 시작했는데, 아무것도 안 나오는 경우
```logs
2024-08-01 15:53:57   VITE v5.3.1  ready in 323 ms
2024-08-01 15:53:57 
2024-08-01 15:53:57   ➜  Local:   http://localhost:5173/
2024-08-01 15:53:57   ➜  Network: http://172.19.0.2:5173/
```
- 접근 허용 범위를 설정한다 (vite.config.js 설정)
  Vite 개발 서버가 모든 네트워크 인터페이스에서 접근 가능하도록 하려면 vite.config.js 파일에서 server.host를 0.0.0.0으로 설정합니다.
```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    host: '0.0.0.0', // 모든 네트워크 인터페이스에서 접근 허용
    port: 5173, // 포트 설정
  }
});

```
