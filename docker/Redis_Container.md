
<!--
##...create a new repository on the command line

__이것은 굵게입니다__ 또는 **이것은 굵게입니다** 
`main` 또는 `master` 코드나 명령어를 강조하려면 백틱(```)을 사용합니다.

방법 1: 줄 끝에 공백 두 개 추가 하면 줄 바뀜
2. 환경 변수 사용    
.env 파일 또는 Spring의 application.yml에서 환경에 맞는

-->

# Docker로 Redis 서버를 실행
    Docker로 Redis 서버를 쉽게 실행하고 관리할 수 있습니다.


### 1. Docker 설치 확인
터미널에 다음 명령어를 입력하여 설치 여부를 확인할 수 있습니다:
```bash
docker --version
````

### 2. Redis 이미지 다운로드
Docker Hub에서 Redis 이미지를 다운로드해야 합니다.

```bash
docker pull redis
````
1. 경로 설명:
   - Redis 이미지가 로컬 머신의 Docker 이미지 저장소에 다운로드됩니다. 
   - 이 이미지가 호스트 시스템의 **Docker 이미지 레지스트리(라이브러리)**에 저장됩니다.
   - Docker가 이미지를 다운로드하고 저장하는 물리적 경로는 일반적으로 사용자가 직접 접근하는 방식으로 관리되지는 않으며, Docker 데몬에 의해 관리됩니다.
   - 실제 이미지는 Docker의 내부 관리 시스템에 저장되며, 사용자가 직접 파일 경로를 지정하거나 변경할 수 없습니다.
2. 시스템별 기본 이미지 저장 경로 :
   - `Linux`: Docker는 기본적으로 /var/lib/docker 디렉터리에 데이터를 저장합니다. 이미지는 overlay2 또는 aufs 같은 Docker의 스토리지 드라이버 하위 폴더에 저장됩니다.
     - 예: /var/lib/docker/overlay2 또는 /var/lib/docker/aufs
   - `Windows`: Docker Desktop을 사용하는 경우, Docker 이미지와 데이터는 WSL 2 (Windows Subsystem for Linux) 환경에 저장됩니다.
     - 일반적으로 WSL 경로는 /mnt/wsl/docker-desktop-data/입니다.
   - `macOS`: Docker Desktop을 사용하면 이미지와 데이터는 내부적으로 Virtual Machine (VM)에 저장됩니다.
     - 경로는 접근이 불가능하며 Docker Desktop이 관리합니다.
3. 이미지 확인 방법:
   - Docker가 관리하는 이미지를 확인하려면 다음 명령어를 사용합니다
   ```bash
   docker images
   ````

### 3. Redis 컨테이너 실행
이미지를 다운로드한 후, Redis 서버를 실행하려면 다음 명령어를 사용합니다:
```bash
docker run --name my-redis -p 6379:6379 -d redis
````
  - my-redis라는 이름으로 Redis 컨테이너실행합니다. 
  - -d 옵션을 사용하여 백그라운드에서 실행합니다.
  - -p 옵션을 사용하여 로컬의 6379 포트를 Redis 컨테이너의 6379 포트에 연결합니다. 

### 4. Redis 컨테이너 상태 확인
```bash
# 모든 컨테이너 목록 확인 (중지된 컨테이너 포함)
docker ps -a

# Redis 컨테이너 상태 확인 (이름 또는 ID)
docker inspect my-redis

# 컨테이너의 로그 확인 (-f 옵션을 추가하면 실시간 모니터링)
docker logs -f my-redis

# Redis 컨테이너 중지
docker stop my-redis

# Redis 컨테이너 재시작
docker restart my-redis
# Redis 컨테이너 삭제
docker rm my-redis

# Redis 이미지 확인
docker images

# Redis 이미지 삭제
docker rmi redis

# 기타
# Redis 컨테이너 안으로 접속 (명령어 실행)
docker exec -it my-redis bash

# Redis 컨테이너 안의 bash 셸로 접속한 후, Redis CLI로 바로 접속
docker exec -it my-redis redis-cli
````
