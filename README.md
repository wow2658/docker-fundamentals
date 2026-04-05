# AI/SW 개발 워크스테이션 구축 결과 보고서

## 1. 프로젝트 개요
- 목표: 코드가 특정 로컬 환경에서만 동작하는 문제를 방지하고, 팀원 누구나 동일하게 재현 가능한 실행 환경 구축.

- 핵심 기술: 리눅스 CLI 기반 권한 제어, Docker를 활용한 컨테이너 격리 및 환경 코드화(Dockerfile), Git/GitHub를 통한 버전 관리 및 협업 환경 구성.

## 2. 실행 환경
- **OS:** macOS (OrbStack 환경)
- **Shell:** zsh
- **Docker:** 28.5.2
- **Git:** 2.53.0

## 3. 수행 항목 체크리스트 및 검증 증거

평가자가 각 수행 항목의 달성 여부와 증거를 한눈에 파악할 수 있도록 체크리스트와 로그를 통합하여 작성했습니다.

### ✅ 3.1 터미널 기본 조작 및 폴더 구성
* **검증 방법:** `pwd`, `mkdir`, `touch`, `cp`, `ls -la` 명령어를 통해 작업 디렉토리 생성 및 파일 복사/이동 확인.
* **수행 로그:**
```bash
# 1. 현재 위치 확인
pwd

# 2. 과제용 격리 폴더 생성 및 이동
mkdir assignment-workspace
cd assignment-workspace
pwd

# 3. 빈 파일 생성
touch empty_file.txt

# 4. 파일에 내용 넣기 및 내용 확인
echo "이건탁 터미널 조작 테스트 데이터입니다." > data.txt
cat data.txt

# 5. 파일 복사
cp data.txt copy_data.txt

# 6. 파일 이동 / 이름 변경
mv copy_data.txt renamed_data.txt

# 7. 목록 확인 (숨김 파일 포함)
ls -al

# 8. 파일 삭제
rm empty_file.txt

# 9. 삭제 후 목록 재확인
ls -al

```
![터미널01스크린샷 2026-04-02 오후 7 39 43](https://github.com/user-attachments/assets/e7ba6417-7242-4d60-9fab-40826f9e34aa)
![터미널02스크린샷 2026-04-02 오후 7 39 43](https://github.com/user-attachments/assets/6d410fa6-36a2-4a7a-ba13-0806d6e1cb4b)
![터미널03스크린샷 2026-04-02 오후 7 39 43](https://github.com/user-attachments/assets/41414d09-1299-4329-96e9-475de3c9803c)


### ✅ 3.2 권한 변경 실습
* **검증 방법:** `chmod` 명령어로 파일 및 디렉토리 권한을 변경하고, `ls -l`로 변경 전후(예: 644 -> 755) 상태 비교.
* **수행 로그:**

```bash
# 10. 권한 실습용 디렉토리 생성
mkdir test_dir

# 11. [디렉토리] 권한 변경 전 확인
ls -ld test_dir

# 12. [디렉토리] 권한 777로 변경 및 변경 후 확인
chmod 777 test_dir
ls -ld test_dir

# 13. [파일] 권한 변경 전 확인
ls -l data.txt

# 14. [파일] 권한 777로 변경 및 변경 후 확인
chmod 777 data.txt
ls -l data.txt
```
<img width="1410" height="824" alt="image" src="https://github.com/user-attachments/assets/f24318f6-7638-410c-b77c-0087fc786311" />

### ✅ 3.3 Docker 설치 및 기본 점검
* **검증 방법:** `docker --version` 및 `docker info` 명령어를 통해 데몬 정상 동작 여부 확인.
* **수행 로그:**

```bash
# 1. 도커 버전 확인
docker --version

# 2. 도커 데몬 정상 동작 점검 (시스템 정보 출력)
docker info
```
<img width="800" height="1626" alt="스크린샷_2026-04-02_오후_8 35 04" src="https://github.com/user-attachments/assets/3baf19b9-0ea7-4c90-b7cb-7f72d2a51ad9" />


### ✅ 3.4 hello-world 및 ubuntu 컨테이너 실행 (기본 운영 명령)
* **검증 방법:** `hello-world` 이미지 실행으로 기본 동작 검증, `ubuntu` 컨테이너 내부 진입 후 명령어 실행 및 `docker ps` 상태 확인.
* **수행 로그:**
```bash
# docker: 도커 엔진과 통신하는 CLI(Command Line Interface) 명령어 시작점.
# run: 새로운 컨테이너를 생성(create)하고 실행(start)하는 하위 명령어.
# hello-world: 도커 설치가 정상적인지 테스트하기 위해 실행하는 초경량 이미지 이름. 로컬에 없으면 원격 레지스트리(Docker Hub)에서 다운로드함.
docker run hello-world

# -i (interactive): 표준 입력(STDIN)을 활성화하여 사용자의 입력을 지속적으로 받을 수 있게 함.
# -t (tty): 가상 터미널 환경(TTY)을 할당하여 쉘(Shell)을 사용할 수 있게 해줌. (일반적으로 -it 형태로 묶어 대화형 조작에 사용함).
# -d (detach): 백그라운드 모드로 실행. 컨테이너를 실행한 뒤 현재 터미널의 제어권을 즉시 사용자에게 돌려줌.
# --name test-attach: 생성될 컨테이너의 이름을 'test-attach'로 명시적으로 지정함 (미지정 시 랜덤 문자열 부여).
# ubuntu:22.04: 베이스로 사용할 이미지 이름(ubuntu)과 버전 태그(22.04). 콜론(:)으로 구분함.
# bash: 컨테이너 내부에서 최초로 실행될 메인 프로세스(PID 1)를 지정. 여기서는 Bash 쉘을 실행함.
docker run -it -d --name test-attach ubuntu:22.04 bash

# 위 명령어와 모든 구성이 동일하며, 컨테이너 이름만 'test-exec'로 다르게 지정하여 백그라운드에 쉘을 실행함.
docker run -it -d --name test-exec ubuntu:22.04 bash

# ps (process status): 현재 실행 중인 도커 컨테이너의 목록을 출력함.
# -a (all): 실행 중인 것뿐만 아니라 중지/종료된 컨테이너까지 모두 포함하여 보여주는 옵션.
docker ps -a

# attach: 실행 중인 컨테이너의 메인 프로세스(PID 1, 여기서는 bash)에 현재 사용자의 터미널 표준 입출력을 연결하여 포그라운드로 끌어옴.
# test-attach: 연결할 컨테이너의 이름.
docker attach test-attach
# exit: 현재 실행 중인 쉘을 종료하는 명령어. 
# 주의: attach 상태에서 exit를 입력하면 컨테이너의 메인 프로세스(PID 1)가 종료되므로, 컨테이너 전체가 중지(Exited) 상태로 변함.
exit

# exec (execute): 이미 실행 중인 컨테이너 내부에 새로운 별도의 프로세스를 추가로 실행함.
# -it: 새로 실행할 프로세스에 가상 터미널과 표준 입력을 할당하여 대화형으로 조작함.
# test-exec: 명령을 실행할 대상 컨테이너 이름.
# bash: 추가로 실행할 쉘 프로그램.
docker exec -it test-exec bash
# exit: exec로 새로 생성된 자식 프로세스(두 번째 bash)만 종료함. 
# 컨테이너의 메인 프로세스(PID 1)는 여전히 살아있으므로 컨테이너 자체는 중지되지 않고 계속 실행 상태(Up)를 유지함.
exit
```
<img width="2038" height="2126" alt="스크린샷 2026-04-02 오후 9 24 29" src="https://github.com/user-attachments/assets/54493dbe-ea7e-4355-82d7-0faf20f562b7" />

### ✅ 3.5 기존 Dockerfile 기반 커스텀 이미지 제작
* **검증 방법:** `nginx:alpine` 베이스 이미지에 커스텀 HTML 파일을 복사하는 Dockerfile 작성 후 `docker build` 성공 확인.
* **수행 로그:**
```bash
# # A 방식 (App 베이스 : NGINX) : 주석문. 애플리케이션(Nginx)이 이미 설치된 이미지를 기반으로 만드는 방법.
# mkdir (make directory): 디렉토리를 생성함.
# -p (parents): 부모 디렉토리가 없으면 함께 생성하며, 이미 디렉토리가 존재해도 에러를 발생시키지 않음.
# src-a: 생성할 디렉토리 이름.
# && (논리 AND): 좌측 명령어(mkdir)가 정상적으로 성공(종료 코드 0)했을 때만 우측 명령어(cd)를 실행하도록 연결함.
# cd (change directory): 현재 작업 위치를 지정한 디렉토리(src-a)로 이동함.
mkdir -p src-a && cd src-a

# echo: 뒤에 오는 텍스트 문자열을 표준 출력으로 화면에 출력함.
# "<h1>...</h1>": HTML 포맷으로 작성된 출력할 문자열.
# > (리다이렉션): 꺾쇠기호. 화면에 출력될 결과를 파일로 방향을 틀어 저장함. 기존 파일이 있으면 내용을 덮어씀.
# index.html: 저장될 파일의 이름.
echo "<h1>Docker Assignment - Method (A) App Base</h1>" > index.html

# -e (enable interpretation of backslash escapes): 백슬래시(\)로 시작하는 이스케이프 문자(\n 등)를 특수 기호로 해석하게 함.
# "FROM nginx:alpine\nCOPY index.html /usr/share/nginx/html/": Dockerfile에 들어갈 내용.
# \n (newline): 줄바꿈 기호. 첫 줄에 FROM 명령어, 두 번째 줄에 COPY 명령어를 배치함.
# FROM nginx:alpine: 도커파일 문법. 베이스 이미지를 매우 가벼운 리눅스 배포판인 알파인(alpine) 기반의 Nginx로 지정.
# COPY [호스트경로] [컨테이너경로]: 도커파일 문법. 현재 호스트에 있는 index.html을 컨테이너 내부의 Nginx 웹 루트 폴더로 복사함.
# > Dockerfile: 위의 두 줄짜리 텍스트를 'Dockerfile' 이라는 이름의 파일로 저장함. (확장자 없음)
echo -e "FROM nginx:alpine\nCOPY index.html /usr/share/nginx/html/" > Dockerfile

# build: 작성된 Dockerfile의 스크립트를 한 줄씩 읽어 새로운 도커 이미지를 생성함.
# -t (tag): 생성될 이미지에 '이름:태그' 형식으로 식별자를 부여함.
# my-web-a:1.0: 이미지 이름은 'my-web-a', 버전 태그는 '1.0'으로 명명.
# . (점 기호): 빌드 컨텍스트(Build Context). Dockerfile과 빌드에 필요한 파일들이 위치한 현재 디렉토리를 도커 엔진에 전달함.
docker build -t my-web-a:1.0 .

# -d: 백그라운드 모드로 실행.
# -p (publish): 포트 포워딩 설정. [호스트_포트]:[컨테이너_포트] 구조.
# 8080:80: 사용자가 컴퓨터의 8080 포트로 접속하면, 컨테이너 내부의 80 포트(Nginx 기본 포트)로 트래픽을 전달함.
# --name test-web-a: 컨테이너 이름을 명시.
# my-web-a:1.0: 실행할 타겟 이미지 및 태그.
docker run -d -p 8080:80 --name test-web-a my-web-a:1.0
```
```bash
# # B 방식 (OS 베이스 : Ubuntu) : 주석문. 깡통 OS(우분투) 이미지를 기반으로 필요한 애플리케이션(Nginx)을 직접 설치하는 방법.
mkdir -p src-b && cd src-b
echo "<h1>Docker Assignment - Method (B) OS Base</h1>" > index.html

# cat: 원래 파일 내용을 출력하는 명령어지만, 리다이렉션 기호와 결합하여 입력을 파일로 만듦.
# << 'EOF' (Here Document): 단일 따옴표로 묶은 'EOF'라는 단어가 단독으로 등장할 때까지 입력되는 모든 다중 줄 문자열을 하나로 묶어 처리함.
# > Dockerfile: 입력받은 다중 줄 문자열을 Dockerfile에 저장함.
cat << 'EOF' > Dockerfile
# FROM ubuntu:22.04: 베이스 이미지 지정. 우분투 22.04 깡통 OS.
FROM ubuntu:22.04
# RUN: 이미지 빌드 과정(컨테이너 내부)에서 쉘 명령어를 실행함.
# apt-get update: 우분투 패키지 저장소의 최신 목록을 가져옴.
# &&: 업데이트 성공 시 우측 명령어 실행.
# apt-get install -y nginx: Nginx 패키지를 설치함. -y(yes) 옵션은 설치 도중 [Y/n] 프롬프트가 뜰 때 무조건 y를 입력하여 자동화를 보장함.
RUN apt-get update && apt-get install -y nginx
# COPY: 로컬 호스트의 index.html을 우분투 환경 내 Nginx 기본 웹 경로(/var/www/html/)로 복사함.
COPY index.html /var/www/html/
# EXPOSE 80: 이 컨테이너가 내부적으로 80번 포트를 사용해 통신할 예정임을 명시함. (실제 포트를 열어주는 방화벽 역할이 아니며, 주로 문서화/메타데이터 제공 목적임).
EXPOSE 80
# CMD: 컨테이너가 처음 실행될 때 기본적으로 구동할 프로세스와 인자를 JSON 배열 형태로 지정함.
# "nginx": 실행 파일.
# "-g", "daemon off;": Nginx는 기본적으로 백그라운드(데몬)로 도는 성질이 있음. 도커는 메인 프로세스가 포그라운드에서 동작하지 않으면 컨테이너를 바로 종료해버림. 따라서 데몬 모드를 끄고 포그라운드에 묶어두는 설정임.
CMD ["nginx", "-g", "daemon off;"]
# EOF (End Of File): Here Document 입력의 끝을 알리는 식별자 (앞서 설정한 'EOF' 문자와 일치해야 함).
EOF

docker build -t my-web-b:1.0 .

# 8081:80: 8080 포트는 이미 앞의 컨테이너가 점유했으므로, 호스트의 8081 포트를 컨테이너의 80 포트로 연결함.
docker run -d -p 8081:80 --name test-web-b my-web-b:1.0
```

<img width="2234" height="2990" alt="스크린샷 2026-04-02 오후 9 33 34" src="https://github.com/user-attachments/assets/78a2b674-ddfc-4896-8251-cb1e6f392105" />

```bash
# images: 로컬 호스트 컴퓨터의 저장소에 다운로드되었거나 빌드된 모든 도커 이미지 목록(크기, 태그, ID 등)을 출력함.
docker images

# 실행 중 및 종료된 모든 컨테이너 목록 확인.
docker ps -a

# logs: 지정된 컨테이너(test-web-a) 내부의 표준 출력(STDOUT) 및 표준 에러(STDERR)에 쌓인 로그 텍스트 기록을 화면에 출력함. 웹 서버의 접속 기록 등을 확인할 때 씀.
docker logs test-web-a

# stats: 지정된 컨테이너들의 리소스 사용량(CPU, RAM 메모리, 네트워크 입출력 등)을 모니터링함.
# --no-stream: 실시간으로 계속 갱신하며 보여주지 않고, 명령어 실행 시점의 데이터만 딱 한 번 출력하고 종료함.
# test-web-a test-web-b test-exec: 모니터링할 타겟 컨테이너 3개의 이름.
docker stats --no-stream test-web-a test-web-b test-exec
```

<img width="2234" height="1508" alt="스크린샷 2026-04-02 오후 9 33 46" src="https://github.com/user-attachments/assets/b2b387bd-fd2f-46d0-814c-069aba4d92ae" />

### ✅ 3.6 포트 매핑 및 브라우저 접속 증거
* **검증 방법:** `-p` 옵션으로 호스트와 컨테이너 포트를 매핑하여 백그라운드(`-d`) 실행 후, `curl` 및 브라우저 접속 확인.
* **수행 로그:**
```bash
curl http://localhost:8080
curl http://localhost:8081
```
* **브라우저 접속 증거:**
*(여기에 http://localhost:8080 에 접속된 브라우저 화면 스크린샷 캡처본 첨부 - 주소창 포함)*
<img width="800" height="238" alt="image" src="https://github.com/user-attachments/assets/479a52c8-ae1f-418e-a60d-a3be63b41a0a" />
<img width="2058" height="630" alt="image" src="https://github.com/user-attachments/assets/d928cea1-d76d-49fc-bb6e-92a6e6123955" />


### ✅ 3.7 바인드 마운트 반영
* **검증 방법:** 호스트의 디렉토리를 컨테이너에 마운트(`-v`)하고, 호스트에서 파일을 수정했을 때 컨테이너에 즉각 반영되는지 확인.
* **수행 로그:**
```bash
# -v (volume): 바인드 마운트(Bind Mount) 설정. 호스트의 디렉토리와 컨테이너 내부의 디렉토리를 직접 연결함. [호스트경로]:[컨테이너경로] 구조.
# $(pwd): 쉘 명령어 치환(Command Substitution) 문법. pwd(print working directory) 명령이 실행된 결과값(현재 디렉토리의 절대경로)을 문자열로 치환하여 삽입함.
# /bind-dir: 호스트의 현재 경로 하위에 있는 bind-dir 디렉토리. (없으면 자동 생성됨).
# :/app: 컨테이너 내부의 최상단 /app 폴더와 연결. 호스트와 컨테이너 양쪽에서 파일 수정 시 즉각적으로 동기화됨.
# sleep infinity: 'infinity(무한대)' 동안 대기(sleep)하라는 명령어. 컨테이너 내부에서 실행할 특별한 서비스가 없더라도, 컨테이너가 죽지 않고 계속 백그라운드에서 살아있도록 강제로 유지하는 꼼수 명령어임.
docker run -d --name bind-test -v $(pwd)/bind-dir:/app ubuntu:22.04 sleep infinity
```
<img width="2626" height="1372" alt="image" src="https://github.com/user-attachments/assets/8cfa285b-cf1d-4a1b-8544-fc8d97dac216" />

### ✅ 3.8 Docker 볼륨 영속성 검증
* **검증 방법:** Docker 볼륨 생성 후 컨테이너에 연결하여 데이터 쓰기 진행. 컨테이너 삭제 후 새 컨테이너에 동일 볼륨 연결 시 데이터 유지 확인.
* **수행 로그:**
```bash
# volume create: 도커 엔진이 직접 물리적 저장 위치를 관리하는 '논리적 스토리지 객체(도커 볼륨)'를 생성함. 호스트 OS 의존성을 줄이고 데이터 관리를 편하게 함.
# my-vol: 생성할 볼륨의 이름.
docker volume create my-vol

# -v my-vol:/data: 바인드 마운트와 달리 콜론(:) 앞부분에 절대경로가 아닌 '생성한 볼륨 이름(my-vol)'을 기입함. 이를 컨테이너 내부의 /data 디렉토리와 연결(마운트)함.
docker run -d --name vol-test -v my-vol:/data ubuntu:22.04 sleep infinity

# rm (remove): 컨테이너를 삭제함.
# -f (force): 컨테이너가 현재 실행 중이더라도, SIGKILL 시그널을 강제로 보내어 프로세스를 즉시 중단시킨 후 삭제함.
docker rm -f vol-test

# 첫 번째 컨테이너가 파괴되었지만 데이터는 도커 볼륨(my-vol)에 안전하게 남아있음. 
# 이를 두 번째 컨테이너(vol-test2)의 /data에 다시 연결(-v)함으로써 데이터의 영속성(Persistence)과 재사용성을 증명함.
docker run -d --name vol-test2 -v my-vol:/data ubuntu:22.04 sleep infinity
```
<img width="2626" height="1206" alt="image" src="https://github.com/user-attachments/assets/fffe0f39-8845-4d71-99d5-a276db2970aa" />

<img width="876" height="498" alt="image" src="https://github.com/user-attachments/assets/26ba3471-bb9b-4be8-ab5a-ab11bdba8f6c" />
<br>
<br>
<br>
<br><br>
<br>

<div align="center">


</div>

 > 컨테이너 마운트 경로(/data)가 어떻게 데이터 동기화(복제)되는거지??
<img width="865" height="375" alt="image" src="https://github.com/user-attachments/assets/c800ee97-a00e-4413-8230-c3760a3e8be8" />
  
* **핵심 전제:** 컨테이너는 가상머신(VM)과 달리 호스트 OS의 커널을 공유하며 네임스페이스(Namespace)에 의해 논리적으로 격리된 단일 프로세스로 동작함. 또한, 컨테이너 내부 파일시스템은 임시 레이어(OverlayFS)로 구성되어 컨테이너 파괴 시 내부 데이터가 영구 소멸(휘발)되는 특성을 지님.
  
<br>
<img width="862" height="361" alt="image" src="https://github.com/user-attachments/assets/cab0b746-2e2e-47a8-9c5b-9a70fd432fd4" />

* **오개념:** 컨테이너 내부 임시 공간에 데이터가 선행 기록된 후, 도커 엔진이 이를 'Docker Volume'이라는 별개의 가상 저장소로 복사 및 동기화한다고 착각함. 이는 I/O 작업 시 양방향 데이터 이동이 발생하여 스토리지 병목 현상 및 동기화 지연을 유발한다는 잘못된 판단으로 직결됨.
  
<br>
<img width="869" height="403" alt="image" src="https://github.com/user-attachments/assets/7763e103-bb64-461f-902e-1f8bbf00596f" />

* **정정:** 도커 볼륨은 독립된 가상 공간이 아닌 호스트 OS 물리 디스크 내부에 생성된 실제 디렉토리임. 마운트된 경로(/data)는 해당 호스트 디렉토리를 가리키는 C언어의 포인터(역참조) 역할을 수행하므로, 데이터 조작 시 복제 비용 발생(Zero-Copy) 없이 호스트 물리 디스크에 즉시 직접 기록됨.

  
<br>
<br>

> 똑같이 -v 쓰는 바인드 마운트와 볼륨. 둘이 뭐가 다른가?
<div align="center">
  


</div>

<img width="789" height="288" alt="image" src="https://github.com/user-attachments/assets/58cfaa28-4a8f-4f04-a44f-d4f8570d714f" />
<img width="796" height="922" alt="image" src="https://github.com/user-attachments/assets/b5593c55-2619-4fcd-9fba-a16db19abca4" />


    
<br>
<br>

### ✅ 3.9 Git 설정 및 GitHub 연동
* **검증 방법:** `git config` 설정 확인 및 VSCode에서 원격 저장소 푸시 완료 확인.
* **수행 로그:**

```bash
# config: Git의 작동 방식이나 사용자 정보를 설정하는 명령어.
# --global: 현재 컴퓨터에 로그인된 사용자 계정 전역(~/.gitconfig)에 설정을 적용함. 어느 디렉토리에서 작업하든 기본값으로 쓰임.
# user.name "이름작성": 커밋(버전 저장)을 남길 때 기록될 작성자의 이름을 "이름작성"으로 지정함. (※제시된 프롬프트의 오류를 수정하여 user.name을 명시함).
git config --global user.name "이름작성"

# user.email "이메일작성": 커밋에 기록될 작성자의 이메일 주소를 "이메일작성"으로 지정함.
git config --global user.email "이메일작성"

# init.defaultBranch main: 앞으로 git init으로 새로운 저장소를 생성할 때, 기본적으로 만들어지는 마스터 브랜치의 이름을 구시대적 단어인 'master' 대신 'main'으로 설정함. Git desktop은 문제가 없는데 TortoiseGit쓸 때 매번 말썽이니 주의할 것!
git config --global init.defaultBranch main

# init (initialize): 비어있는 새로운 Git 저장소를 현재 디렉토리에 생성함. 내부적으로 .git 이라는 숨김 폴더가 만들어지며 버전 관리가 시작됨.
git init

# remote add: 로컬 저장소와 연결될 외부 클라우드 원격 저장소 주소를 추가함.
# origin: 길고 복잡한 원격 저장소 URL을 매번 치기 힘드므로, 관례적으로 붙이는 '기본 원격 저장소의 별칭(닉네임)'.
# https://github...: 연동할 실제 GitHub 리포지토리의 웹 주소.
git remote add origin https://github.com/wow2658/docker-fundamentals.git

# branch: 깃의 브랜치(가지)를 생성하거나 관리하는 명령어.
# -M (Move/Rename): 현재 체크아웃되어 있는 브랜치의 이름을 강제로 변경함 (기존 이름이 뭐든 상관없이 덮어씀).
# main: 변경할 브랜치의 새 이름. (최신 깃허브 정책에 맞춰 로컬 브랜치 이름도 main으로 확실히 맞추는 작업).
git branch -M main
```
<img width="1517" height="1034" alt="image" src="https://github.com/user-attachments/assets/b2686d40-d299-4e3e-9d9c-9f3e0907024f" />

* **연동 증거:**
<img width="1377" height="600" alt="image" src="https://github.com/user-attachments/assets/6448c2a4-4ce1-4284-ac58-9207afc058fb" />


<div align="center">

**< GitHub 원격 레포지토리 생성 시 주의사항 (Initial Commit 충돌 방지) >**

</div>

* **원칙:** GitHub 웹에서 새 레포지토리(New repository) 생성 시 `Add a README file`, `Add .gitignore`, `Choose a license` 항목은 **모두 체크 해제**할 것.
* **이유:** 해당 옵션을 하나라도 체크하면 GitHub 서버 측에 자동으로 'Initial commit'이 생성되며 파일이 추가됨.
* **발생 문제:** 이미 로컬 환경에서 `git init`으로 시작해 작업(커밋)을 마친 내역을 원격에 연결(`git remote add origin`)하여 푸시(`git push`)하려 할 때, 로컬과 원격의 커밋 시작점(뿌리)이 서로 달라 푸시가 거부(Rejected)되고 히스토리가 꼬이는 현상 발생.
* **올바른 순서:** 아무 파일도 없는 완전한 빈(Empty) 레포지토리를 생성한 후, 로컬에서 작업한 내역을 최초 푸시하여 커밋 히스토리를 로컬 기준으로 동기화해야 함.

---

## 4. 트러블슈팅 (3건)

**[트러블슈팅 1] 다중 컨테이너 포트 매핑 혼란(매직 넘버)과 네이밍 규칙 도입**
* 문제 상황: 로컬 도커에 다수의 마이크로서비스를 띄우면서 -p 8080:80, -p 8081:80 방식으로 호스트 포트를 무작위 할당함. 점차 어떤 포트가 어떤 앱인지 외우지 못해 접속 주소를 헷갈리거나 엉뚱한 포트에 접근해 디버깅 시간을 허비하는 불편함을 겪음.
* 원인 분석: 포트를 직관적이지 않은 숫자에만 의존하여 관리하는 하드코딩 방식 자체가 휴먼 에러를 유발하는 근본 원인이라 판단함.
* 해결 및 대안: 매번 docker ps로 포트를 확인하는 수고를 덜기 위해, 컨테이너 실행 시 --name test-web-a-8080과 같이 별명에 호스트 포트를 명시적으로 박아넣는 네이밍 컨벤션을 강제하여 직관성을 1차적으로 개선함.
<img width="861" height="512" alt="image" src="https://github.com/user-attachments/assets/ebfba12f-c9e8-4114-ac79-3bb14a1df54c" />

<br>
<br>
<br>

**[트러블슈팅 2] 실서비스 배포 환경(Production)과의 괴리 해결 및 Nginx 리버스 프록시 도입**
* 문제 상황: 컨테이너 이름에 포트를 명시하여 혼란은 줄였으나, 여전히 브라우저에 localhost:8080과 같은 숫자를 타이핑해야 했음. 처음에는 이를 ngrok 같은 임시 외부 개방 터널링 툴의 용도와 혼동하여 로컬 환경에서의 도메인 매핑이 불필요한 꼼수라고 오인함.
* 원인 분석: ngrok은 외부 접속을 뚫어주는 임시 툴인 반면, Nginx 리버스 프록시는 실서비스 배포 환경과 100% 동일한 라우팅 아키텍처를 로컬에 모사하기 위한 필수 인프라 설계임을 깨달음. 로컬에서 포트 기반 매핑을 방치하면 추후 정식 도메인 배포 시 CORS 에러 및 API 엔드포인트 전면 수정이라는 막대한 병목이 발생함을 파악함.
* 해결 및 대안: 도커 네트워크 최전방에 Nginx 리버스 프록시 컨테이너를 배치하고, 로컬 DNS(hosts)를 조작해 http://app-a.local 이라는 가상 도메인으로 내부 라우팅되게 구성함. 이를 통해 포트 암기 지옥에서 벗어남과 동시에, 추후 소스 코드 수정 없이 프록시의 타겟 도메인만 변경하면 되는 유연한 운영 배포 기반을 구축함.
<img width="968" height="452" alt="image" src="https://github.com/user-attachments/assets/521163f2-74f2-40e0-8baa-9aa3ccebd212" />
<br>
<br>
<br>

**[트러블슈팅 3] 좀비 컨테이너로 인한 이름 충돌(Conflict) 및 수동 삭제 자동화**
* 문제 상황: 코드나 Dockerfile 수정 후 docker run을 재실행할 때마다 Error response from daemon: Conflict. 에러가 발생하며 실행이 거부됨. 이전 테스트 컨테이너가 찌꺼기처럼 남아 이름을 점유하고 있어 매번 docker rm으로 수동 삭제해야만 했음.
* 원인 분석: 컨테이너는 실행이 종료(Exited 상태)되어도 사용자가 명시적으로 지우기 전까지는 로그 확인 등을 위해 시스템 리소스와 네임스페이스를 계속 점유한다는 도커의 생명주기 특성을 간과함.
* 해결 및 대안: 단순 기능 검증용으로 일회성 실행되는 테스트 컨테이너는 종료 후 보존할 가치가 없다고 판단, 런타임 명령어에 --rm 옵션을 도입함. 컨테이너 중지 즉시 도커 데몬이 리소스와 이름을 자동 휘발시키게 하여, 수동 삭제 과정을 없애고 즉각적인 무한 재실행 파이프라인을 세팅함.

<img width="964" height="816" alt="image" src="https://github.com/user-attachments/assets/41dd60e5-ba75-4c52-bdee-80944ecde4e3" />
<br>
<br>

## 5. 학습 개념 정리

**1) 절대 경로와 상대 경로의 차이**
* **절대 경로:** 파일 시스템의 최상위 루트(`/`) 또는 홈 디렉토리(`~`)를 시작점으로 하는 전체 경로. 작업 위치와 무관하게 고정된 대상을 가리킴. (예: `/Users/user/workspace/src`)
* **상대 경로:** 현재 작업 중인 디렉토리(PWD)를 기준으로 한 경로. 위치 이동 시 대상이 변경됨. `.`(현재 위치), `..`(상위 폴더) 사용함. (예: `../test_dir/data.txt`)

**2) 파일 권한(r/w/x)과 8진수(Octal) 표기법**
권한 문자열(10자리)의 첫 글자는 파일 유형(`d`: 디렉토리, `-`: 일반 파일)을 나타냄. 나머지 9자리는 소유자, 그룹, 기타 사용자로 나뉘며, 2진수 비트 연산을 기반으로 한 8진수(0~7)로 압축 표기함. (읽기 r=4, 쓰기 w=2, 실행 x=1)
* **644 (`-rw-r--r--`):** 맨 앞 `-`(일반 파일) + 6(4+2 -> 110), 4(100), 4(100) 조합. 소유자만 읽기/쓰기가 가능하며 나머지는 읽기 전용. (일반 텍스트 파일 표준)
* **755 (`drwxr-xr-x`):** 맨 앞 `d`(디렉토리) + 7(4+2+1 -> 111), 5(4+1 -> 101), 5(4+1 -> 101) 조합. 소유자는 모든 권한을 가지며 나머지는 읽기 및 실행(디렉토리 진입)만 가능. (디렉토리 및 실행 파일 표준)


**3) 기존 Dockerfile 기반 "커스텀 이미지" 제작**
`FROM`으로 검증된 베이스 이미지(예: `nginx:alpine`)를 불러온 뒤, `COPY`로 로컬 파일을 덮어씌우거나 `RUN`으로 환경을 변형하여, 프로젝트 목적에 맞는 독립적인 실행 도면(Image)을 구축하는 과정.

**4) 포트 매핑의 필요성**
컨테이너는 호스트와 격리된 독립 가상 네트워크를 사용함. 외부에서 컨테이너 내부 프로세스(웹 서버 등)에 접근하려면, 호스트의 특정 포트와 컨테이너의 포트를 연결(포워딩)하는 `-p` (Publish) 설정이 필수적임.

**5) Docker 볼륨 (데이터 영속성)**
컨테이너 삭제 시 내부 데이터도 함께 휘발(Ephemeral)되는 문제를 해결하기 위해, 호스트의 특정 디렉토리나 도커 엔진이 관리하는 스토리지 영역을 컨테이너에 마운트하여 데이터를 영구 보존하는 기술.

**6) Git과 GitHub의 역할 차이**
* **Git:** 로컬 환경에서 소스 코드의 버전 이력을 추적하고 관리하는 분산형 버전 관리 시스템(VCS).
* **GitHub:** Git 로컬 저장소를 클라우드에 업로드하여 백업하고, 원격 협업(Pull Request 등)을 지원하는 소스코드 호스팅 플랫폼.
