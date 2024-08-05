# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

<br>

## 문서

- [Docker 프론트엔드 설정](docker_frontend.md)

프론트엔드에 Docker를 설정하는 방법에 대한 자세한 내용은 [Docker 프론트엔드 설정](docker_frontend.md) 가이드를 참조하세요.


# node js App 기본형 생성

1) node js 설치
   http://nodejs.org/en 에서 다운로드 후 설치(기본형으로)
```sh
node -v
```

2) 패키지 설치
- [-g] 인 경우 clobal 패키지로 설치되어 어디서나 app 패키지를 설치할수 있음
```sh
cd <프로젝트폴더>
npm install -g create-react-app
```

3) 프로젝트 생성
```sh
create-react-app management
```

4) 프로젝트가 정상적으로 성공하면 실행방법이 타이핑된다.
```sh
cd management
yarn start
```
- yarn이 설치되어 있지 않을 경우 설치
```sh
npm install -g yarn
```
5) http://localhost:3000

<br>

# vite@latest 를 이용하여 프로젝트 생성

1) 프로젝트 생성
```sh
npm create vite@latest
project name: slee-hotel (프로젝트폴더, 이하에 소스생성됨)
select a framework :
	React 선택
select a variant :
	JavaScript 선택	
```

2) 만들어진 프로젝트로 들어가서 의존성 설치
```sh
cd .<만들어진 프로젝트>
npm install
```

3) install 이 완료되면, 프로젝트 안에 각종 파일들이 생성된다. 이 후 run
```sh
npm run dev 
npm install
```

4) index.css 파일을 붙여넣기

<br>

# 새로운 디렉토리에 Vite 프로젝트 생성 후 통합
1) git pull 프로젝트 생성
- rentRoom-client 생성

2) vite@latest 생성 후 rentRoom-client 통합
```sh
npm create vite@latest rentRoom-client
```
```markdown
  select a framework :
	React 선택
  select a variant :
	JavaScript 선택
```
```sh
cd rentRoom-client
npm install
npm run dev
```
```markdown
  VITE v5.3.1  ready in 1347 ms
  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```



<br>

# git pull 프로젝트 생성 이후 수행 단계

1. 의존성 설치 확인
- npm install 명령어를 실행하여 package.json에 명시된 모든 의존성을 설치
```sh
npm install
```

2. react 스크립트 확인
- package.json 파일에 dev 스크립트가 정의되어 있는지 확인 (devDependencies- react관련 lib 확인, 'dev'기 있는지 확인 )

3. 서버 실행
- npm run dev를 실행할 때 발생하는 오류 메시지를 확인하고 그에 따라 문제를 해결
```sh
npm run dev
```

4. 실패시에 모듈 재설치
- Node.js와 npm 버전 확인
```sh
node -v
npm -v
```
- npm 캐시 클린 및 모듈 재설치
```sh
npm cache clean --force
rm -rf node_modules
npm install
```


5. 프로젝트의 package 라이브러리 확인
- package.json
```sh
{
  "devDependencies": {
    "@types/react": "^x.x.x",
    "@types/react-dom": "^x.x.x",
    "@vitejs/plugin-react": "^x.x.x",
    "eslint": "^x.x.x",
    "eslint-plugin-react": "^x.x.x",
    "eslint-plugin-react-hooks": "^x.x.x",
    "eslint-plugin-react-refresh": "^x.x.x",
    "vite": "^x.x.x"
  },
  "dependencies": {
    "@popperjs/core": "^x.x.x",
    "axios": "^x.x.x",
    "bootstrap": "^x.x.x",
    "cors": "^x.x.x",
    "date-fns": "^x.x.x",
    "jwt-decode": "^x.x.x",
    "moment": "^x.x.x",
    "react": "^x.x.x",
    "react-bootstrap": "^x.x.x",
    "react-date-range": "^x.x.x",
    "react-dom": "^x.x.x",
    "react-icons": "^x.x.x",
    "react-router-dom": "^x.x.x",
    "reactstrap": "^x.x.x"
  }
}
```
For a detailed explanation of the `package.json` file, see [this document](docs/package-json-explanation.md).

- dependencies : 애플리케이션이 실행될 때 필요한 패키지
```sh
npm install <package-name> 또는 yarn add <package-name>
```

- devDependencies : 애플리케이션을 개발하는 동안에만 필요한 패키지
```sh
npm install <package-name> --save-dev 또는 yarn add <package-name> --dev
```
- 프로덕션 환경에서는 dependencies만 설치
```sh
npm install --only=production 또는 yarn install --production
```

- 예) 프로젝트의 package 에 라이브러리 추가
```sh
추가
npm install axios --save-dev   <-- db에 바로 접속하기 위해서 사용
npm install axios bootstrap react react-bootstrap react-dom --save  <-- react 사용
npm install axios bootstrap react react-bootstrap react-dom --save-dev
npm install react-icons react-router-dom --save-dev <-- icon사용, 네비게이트 링크등 사용

제거
npm uninstall axios bootstrap react react-bootstrap react-dom --save
npm uninstall webpack babel-core eslint jest --save-dev
```
