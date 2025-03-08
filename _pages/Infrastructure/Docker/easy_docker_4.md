---
title: "도커(Docker)\bDive - 주요 명령어와 지시어"
tags:
  - Docker
date: 2025-02-04
thumbnail: ../../../assets/img/post_img/easy_docker_img/easy_docker_logo.png
---

## 도커 명령어
- 기본 양식: `docker (Management Command) Command`
	- Management Command는 생략 가능 (생략이 가능하면 생략을 권장)
- **정보**
	- `docker version` : Client, Server의 버전 및 상태 확인
	- `docker info` : 플러그인, 호스트 OS의 시스템 상세 정보 확인
	- `docker --help` : 메뉴얼 확인
		- e.g.
			- `docker --help`
			- `docker container --help`
			- `docker container run --help`
	- `docker ps` : 실행 중인 컨테이너 리스트 조회
		- `-a` : 종료된 컨테이너 포함 모든 컨테이너 조회
	- `docker logs (컨테이너 명)` : 실행 중인 컨테이너의 로그 조회
		- `-f` : 실시간 로그 조회
- **이미지 레지스트리**
	- `docker pull 이미지명` : 로컬 스토리지로 이미지 다운로드 (이미지 네이밍 규칙 준수)
	- `docker tag 기존이미지명 추가할이미지명` : 로컬 스토리지에 이미지명 추가
		- **실제 파일은 하나** (즉, 하나에 이미지에 여러 개의 이름 추가 가능)
		- 같은 파일이어도 이름에 따라 어디에 업로드 될 지가 달라짐
		- e.g. `docker tag devwikirepo/simple-web:1.0 veluga29/my-simple-web:0.1`
	- `docker push 이미지명` : 이미지 레지스트리에 이미지 업로드
	- `docker login` : 로컬 스토리지 특정 공간에 이미지 레지스트리 **인증 정보** 생성 
		- 생성 디렉터리: `~/.docker/config.json`
	- `docker logout` : 이미지 레지스트리 인증 정보 삭제
- Management Command - **`container`**
	- `docker run (실행 옵션) 이미지명 (실행명령)` : 컨테이너 실행
		- `-d` : 백그라운드 실행 (데몬 프로그램 실행에 적합)
		- `--name {컨테이너명}` : 컨테이너의 이름 지정
		- `-it` : 커맨드 창을 통해 실행할 컨테이너와 직접 상호작용
			- shell 명령 `bin/bash` 추가 필요
		- `--network 네트워크명` : 원하는 네트워크 지정
		- `-p HostOS의포트:컨테이너의포트` : 포트포워딩 옵션
		- `-v 도커의볼륨명:컨테이너의내부경로` : 볼륨 마운트
			- e.g. 
				- `-v volume1:/var/lib/postgresql/data`
				- `-v volume1:/etc/postgresql -v volume2:/var/lib/postgresql/data`
		- `-v 사용자지정HostOS디렉토리:컨테이너의내부경로` : 볼륨 바인드 마운트 (디버깅용)
			- e.g. `-v volume1:/var/lib/postgresql/data`
		- `--cpus={CPUcore수}` : 컨테이너가 사용할 최대 CPU 코어 수 (소수점도 가능)
		- `--memory={메모리용량}` : 컨테이너가 사용할 최대 메모리 정의 (b, k, m, g 단위)
			- e.g. `docker run --cpus=1 --memory=8g`
		- e.g.
			- `docker run 이미지명 (실행명령)` : 컨테이너 실행 시 메타데이터의 cmd 덮어쓰기
			- `docker run --env KEY=VALUE 이미지명` : 컨테이너 실행 시 메타데이터의 env 덮어쓰기
			- `docker run -it --name 컨테이너명 이미지명 bin/bash` : 컨테이너 실행과 동시에 터미널 접속 (shell) - **이미지 내부 파일 시스템 확인 혹은 디버깅 용도**
			- `docker run -it --network second-bridge --name ubuntuC devwikirepo/pingbuntu bin/bash` : 원하는 네트워크 지정해 컨테이너 실행
			- `docker run -d --name my-postgres -e POSTGRES_PASSWORD=password -v mydata:/var/lib/postgresql/data postgres:13` : 볼륨 지정해 DB 실행
	- `docker rm 컨테이너명/ID` : 컨테이너 삭제
		- `-f` : **실행 중**인 컨테이너 삭제 (단순 `rm`은 실행 중인 컨테이너 삭제 불가)
		- e.g.
			- `docker rm -f multi1 multi2 multi3` : 여러 컨테이너 한번에 삭제
	- `docker cp 원본위치 복사위치` : 컨테이너와 호스트 머신 간 파일 복사
		- `docker cp 컨테이너명:원본위치 복사위치` : 컨테이너 -> 호스트머신으로 파일 복사
		- `docker cp 원본위치 컨테이너명:복사위치` : 호스트머신 -> 컨테이너로 파일 복사
	- `docer container inspect 컨테이너명` : 컨테이너의 메타 데이터 조회
		- 결과 예시
			```json
			[{
				{
				...
				"NetworkSettings": {
					...
					"Networks": {
							"bridge": { //브릿지 네트워크명
								...
								"Gateway": "172.17.0.1", //도커브릿지 가상 IP
			                    "IPAddress": "172.17.0.2", //컨테이너 가상 IP
			                    ...
							}
						}
					}
				}
			}]
			```
	- `docker stats (컨테이너명/ID)` : 컨테이너의 리소스 사용량 조회
	- `docker events` : Host OS에서 발생하는 컨테이너 관련 이벤트 로그 조회
- Management Command - **`image`**
	- `docker image ls (이미지명)` : 다운로드된 이미지 조회
	- `docker image inspect 이미지명` : 이미지의 메타 데이터 조회
	- `docker image rm 이미지명` : 로컬 스토리지의 이미지 삭제
	- `docker image history 이미지명` : 이미지의 레이어 이력 조회
- 도커 커밋
	- `docker commit -m 커밋명 실행중인컨테이너명 생성할이미지명` : 실행 중인 컨테이너를 이미지로 생성
- 도커 빌드
	- `docker build -t 이미지명 Dockerfile경로` : 도커파일을 통해 이미지 빌드
		- `Dockfile경로` = 빌드 컨텍스트 지정
		- **도커 파일이 있는 경로로 가서 실행**하자! (`Dockerfile경로`=`.`)
	- 옵션
		- `-t 이미지명` : 결과 이미지의 이름 지정
		- `-f 도커파일명`
			- 도커파일명이 Dockerfile이 아닌 경우 별도 지정
			- **케이스 별**로 **다른 도커파일**이 필요한 경우
		- `--no-cache` : 캐시를 사용하지 않고 빌드
			- e.g. `docker build -t leafy:2.0.0 . --no-cache`
- Management Command - **`network`**
	- `docker network ls` : 네트워크 리스트 조회
	- `docker network inspect 네트워크명` :  네트워크 상세 정보 조회
	- `docker network create 네트워크명` : 네트워크 생성
		- e.g. `docker network create --driver bridge --subnet 10.0.0.0/24 --gateway 10.0.0.1 second-bridge`
	- `docker network rm 네트워크명` : 네트워크 삭제
- Management Command - **`volume`**
	- `docker volume ls` : 볼륨 리스트 조회
	- `docker volume inspect 볼륨명` : 볼륨 상세 정보 조회
		- e.g.
			```json
			[
			    {
			        "CreatedAt": "2025-02-05T04:38:44Z",
			        "Driver": "local", //local = 실제 데이터가 호스트 OS에 저장됨
			        "Labels": {},
			        //경로는 리눅스에서 관찰 가능, MacOS 등은 관찰 불가
			        //도커가 가상 머신 형태로 실행되기 때문
			        "Mountpoint": "/var/lib/docker/volumes/mydata/_data",
			        "Name": "mydata",
			        "Options": {},
			        "Scope": "local"
			    }
			]
			```
	- `docker volume create 볼륨명` : 볼륨 생성
	- `docker volume rm 볼륨명` : 볼륨 삭제
- Management Command - **`compose`**
	- `docker compose up -d` : YAML 파일에 정의된 서비스 생성 및 시작
		- `--build` : 로컬에 동일 이름 이미지가 있으면 제거하고 새 이미지로 다시 빌드
			- 소스코드 변경이 있어야 적용됨
	- `docker compose ps` : 현재 실행 중인 서비스 상태 표시
	- `docker compose build` : 현재 실행 중인 서비스의 이미지만 빌드
	- `docker compose logs` : 실행 중인 서비스의 로그 표시
	- `docker compose down` : YAML 파일에 정의된 서비스 종료 및 제거
		- `-v` : 볼륨까지 함께 제거 (옵션이 없으면 기본적으로 볼륨은 남아있음)

## Dockerfile 지시어
- 기본 양식: `지시어 지시어의옵션`
- 유의사항
	- 일반적으로 파일 시스템 변 경이 있는 명령어는 레이어 추가 O
	- 메타데이터에만 영향 주는 명령어는 레이어 추가 X
- 기본 지시어
	- `FROM 이미지명` : 베이스 이미지를 지정 (**필수**)
	- `COPY 빌드컨텍스트내파일경로 복사할레이어경로` : 파일을 레이어에 복사 (**새로운 레이어 추가**)
		- `--from` : 파일을 가져올 다른 스테이지 지정 (멀티 스테이지 빌드)
			- 즉, 빌드 컨텍스트가 아니라 다른 스테이지 이미지에서 파일을 가져옴
			- e.g. `--from=build`
- 시스템 관련 지시어
	- `WORKDIR 디렉터리명` : 작업 디렉터리를 지정 (**새로운 레이어 추가**, `cd`)
		- 다음에 나오는 지시어들은 지정된 디렉터리 기준으로 수행됨
		- 가능한 **초반**에 `FROM` 다음 **바로 작성**하는 것이 좋음
	- `USER 유저명` : 명령을 실행할 사용자 변경 (**새로운 레이어 추가**, `su`)
		- **기본**은 **루트 사용자**로 실행
		- 보안을 위해 컨테이너가 필요 이상의 권한을 가지지 않도록 조절
	- `EXPOSE 포트번호` : 컨테이너가 사용할 포트를 명시
		- 보통은 소스 코드안에 애플리케이션이 사용할 포트가 지정되어 있음
		- 따라서, 필수는 아니지만 공유 **문서 기재 용도** 큼 (도커파일만 보고도 포트 확인 가능)
- 환경변수 설정
	- `ARG 변수명 변수값` : 이미지 빌드 시점에만 사용할 환경 변수 설정
		- `docker build --build-arg 변수명=변수값` : 덮어쓰기
	- **`ENV 변수명 변수값`** (권장) : 이미지 빌드 및 컨테이너 실행 시점까지 계속 유지될 환경 변수 설정
		- `docker run -e 변수명=변수값` : 덮어쓰기
- 프로세스 실행
	- `ENTRYPOINT ["명령어"]` : 자주 쓰이는 고정된 명령어를 지정
		- 의도치 않은 명령어 접근 1차적 방지 (완벽 X)
		- e.g. `ENTRYPOINT ["npm"]`
	- `CMD ["명령어"]` : 컨테이너 실행 시 실행 명령어 지정 (메타 데이터 CMD 필드에 저장됨)
		- e.g. `CMD ["start"]`
	- `RUN 명령어` : 명령어 실행 (**새로운 레이어 추가**)

## Docker Compose 지시어
예시 1 - 애플리케이션 & Redis
```yaml
version: '3'
services:
  hitchecker:
    build: ./app
	image: hitchecker:1.0.0
	ports:
	  - "8080:5000"
  redis:
    image: "redis:alpine"
```
예시 2 - 이중화 DB
```yaml
version: '3'
x-environment: &common_environment
  POSTGRESQL_POSTGRES_PASSWORD: adminpassword
  POSTGRESQL_USERNAME: myuser
  POSTGRESQL_PASSWORD: mypassword
  POSTGRESQL_DATABASE: mydb
  REPMGR_PASSWORD: repmgrpassword
  REPMGR_PRIMARY_HOST: postgres-primary-0
  REPMGR_PRIMARY_PORT: 5432
  REPMGR_PORT_NUMBER: 5432

services:
  postgres-primary-0:
    image: bitnami/postgresql-repmgr:15
    volumes:
	  - postgres_primary_data:/bitnami/postgresql
	environment:
	  <<: *common_environment
	  REPMGR_PARTNER_NODES: postgres-primary-0,postgres-standby-1:5432
	  REPMGR_NODE_NAME: postgres-primary-0
	  REPMGR_NODE_NETWORK_NAME: postgres-primary-0
  postgres-standby-1:
	image: bitnami/postgresql-repmgr:15
	volumes:
	  - postgres_standby_data:/bitnami/postgresql
	environment:
	  <<: *common_environment
	  REPMGR_PARTNER_NODES: postgres-primary-0,postgres-standby-1:5432
	  REPMGR_NODE_NAME: postgres-standby-1
	  REPMGR_NODE_NETWORK_NAME: postgres-standby-1
volumes:
  postgres_primary_data:
  postgres_standby_data:
```
예시 3 - Leafy
```yaml
version: '3'
services:
  leafy-postgres:
    build: ./leafy-postgresql
    image: leafy-postgres:5.0.0-compose
    volumes:
      - mydata:/var/lib/postgresql/data
	deploy:
	  resources:
	    limits:
	      cpus: '1'
	      memory: 256M
	restart: always

  leafy-backend:
    build: ./leafy-backend
    image: leafy-backend:5.0.0-compose
    environment:
	  - DB_URL=leafy-postgres
    depends_on:
      - leafy-postgres 
    deploy:
	  resources:
	    limits:
          cpus: '1.5'
		  memory: 512M
    restart: on-failure

  leafy-front:
    build: ./leafy-frontend
    image: leafy-front:5.0.0-compose
    environment:
	  - BACKEND_HOST=leafy-backend
    ports:
	  - 80:80
    depends_on:
	  - leafy-backend
    deploy:
      resources:
	    limits:
	      cpus: '0.5'
	      memory: 64M
    restart: on-failure

volumes:
  mydata:
```
- `version` : 도커 컴포즈의 버전 정의
- `services` : 실제로 실행할 컨테이너들의 리스트
	- `컨테이너 이름`
		- `build` : 이미지 빌드가 필요한 경우 지정 (**도커파일 경로 지정**)
		- `image` : 원하는 이미지 지정
			- 기존 이미지가 있는 경우 그대로 사용 (e.g. `hitchecker:1.0.0`)
			- 없거나 `--build` 옵션 적용할 땐 `build` 경로의 Dockerfile 사용해 이미지 빌드
				- e.g. `docker build -t hitchecker:1.0.0 ./app`
				- 이미지 재빌드 : 이미지 태그를 바꾸기 or `--build` 옵션 사용
			- 기존 이미지도 없고 `build` 경로도 없는 경우, 외부 이미지 다운
		- `ports` : `-p` 옵션과 동일 (포트 포워딩)
		- `volumes` : 마운트할 볼륨 지정
			- `볼륨명` : `컨테이너내부경로`
		- `environment` : 환경변수 지정
			- `키 : 밸류` : 기본방식
			- `<<: *common_environment` : `x-environment`의 공통 환경변수 주입
		- `depends-on` : 특정 컨테이너가 실행될 때까지 컨테이너 실행 보류
			- 없으면 모든 컨테이너가 병렬 실행
			- 다만, 이렇게 지정해도 프로세스 실행 속도 차이로 문제 발생 가능
			- -> 대신 물리적으로 일정 시간을 정해두는 방법이 좋을 수도 있음
- `volumes` : 생성할 볼륨의 리스트
- `x-environment: &common_environment` : 공통 환경변수 지정 (도커 컴포즈 버전 3 이상)

***
## Reference
[개발자를 위한 쉬운 도커](https://www.inflearn.com/course/%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-%EC%89%AC%EC%9A%B4-%EB%8F%84%EC%BB%A4)