# AI/SW 개발 워크스테이션 구축 결과 보고서 초안

## 1. 프로젝트 개요
특정 로컬 호스트 종속성 문제를 해결하고, 팀원 누구나 재현 가능한 실행 환경을 구성하기 위한 '개발 워크스테이션 구축'을 목표로 함. 리눅스 CLI 기반의 권한 제어, Docker를 활용한 컨테이너 격리 및 포트 매핑, 그리고 인프라 환경의 문서화 능력을 검증함.

## 2. 실행 환경
- **OS:** macOS (OrbStack 환경)
- **Shell:** zsh
- **Docker:** 28.5.2
- **Git:** (터미널에서 `git --version` 확인 후 숫자 기입)

## 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습 (파일/디렉토리)
- [x] Docker 설치 및 데몬 점검
- [x] 커스텀 이미지 제작 및 포트 매핑 접속
- [x] hello-world / ubuntu 컨테이너 실행 및 차이 관찰
- [x] 바인드 마운트 반영 증거
- [x] Docker 볼륨 영속성 검증
- [x] Git 설정 및 GitHub 연동

---

## 4. 수행 검증 로그 및 증거 자료

### 4.1 터미널 조작 및 권한 실습 로그
```bash
# 1. 터미널 조작
user@workstation ~ % pwd
/Users/user
user@workstation ~ % mkdir assignment-workspace
user@workstation ~ % cd assignment-workspace
user@workstation ~/assignment-workspace % touch empty_file.txt
user@workstation ~/assignment-workspace % echo "이건탁 터미널 조작 테스트 데이터입니다." > data.txt
user@workstation ~/assignment-workspace % cat data.txt
이건탁 터미널 조작 테스트 데이터입니다.
user@workstation ~/assignment-workspace % cp data.txt copy_data.txt
user@workstation ~/assignment-workspace % mv copy_data.txt renamed_data.txt
user@workstation ~/assignment-workspace % ls -al
user@workstation ~/assignment-workspace % rm empty_file.txt

# 2. 권한 변경 전/후 비교 검증
user@workstation ~/assignment-workspace % mkdir test_dir
user@workstation ~/assignment-workspace % ls -ld test_dir
drwxr-xr-x  2 user  user  64  4  2 19:49 test_dir
user@workstation ~/assignment-workspace % chmod 777 test_dir
user@workstation ~/assignment-workspace % ls -ld test_dir
drwxrwxrwx  2 user  user  64  4  2 19:49 test_dir

user@workstation ~/assignment-workspace % ls -l data.txt
-rw-r--r--  1 user  user  57  4  2 19:49 data.txt
user@workstation ~/assignment-workspace % chmod 777 data.txt
user@workstation ~/assignment-workspace % ls -l data.txt
-rwxrwxrwx  1 user  user  57  4  2 19:49 data.txt
```

### 4.2 Docker 설치 및 기본 점검
```bash
user@workstation ~/assignment-workspace % docker --version
Docker version 28.5.2, build v28.5.2

user@workstation ~/assignment-workspace % docker info
Client:
 Version:    28.5.2
 Context:    orbstack
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Path:     /Users/user/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Path:     /Users/user/.docker/cli-plugins/docker-compose
Server:
 Server Version: 28.5.2
 Operating System: OrbStack
 Architecture: x86_64
```

### 4.3 커스텀 이미지 제작 및 포트 매핑
* **커스텀 포인트 요약:** (A) 웹 서버 베이스 이미지 활용 (`nginx:alpine`). 경량화된 NGINX 웹 서버 환경을 상속받고, `COPY` 명령어로 커스텀 `index.html`을 컨테이너의 기본 웹 루트 경로(`/usr/share/nginx/html/`)에 덮어씌움. 이를 통해 포트 매핑(8080:80) 통신 성공 여부를 직관적인 문구로 확인하기 위함.

```bash
user@workstation ~/assignment-workspace % mkdir src && cd src
user@workstation ~/assignment-workspace/src % echo "<h1>Docker Assignment Web Server (Port Mapping)</h1>" > index.html
user@workstation ~/assignment-workspace/src % echo -e "FROM nginx:alpine\nCOPY index.html /usr/share/nginx/html/" > Dockerfile
user@workstation ~/assignment-workspace/src % docker build -t my-custom-web:1.0 .
user@workstation ~/assignment-workspace/src % docker run -d -p 8080:80 --name test-web my-custom-web:1.0
user@workstation ~/assignment-workspace/src % docker ps
```

**[포트 매핑 브라우저 접속 증거]**
---

## 5. 트러블슈팅 (2건)

**[트러블슈팅 1] Zsh 대화형 쉘에서의 주석(`#`) 파싱 에러**
* **문제 상황:** 터미널 기본 조작 실습 중, 주석(`# 1. 현재 위치 확인`)이 포함된 명령어를 복사/붙여넣기 하자 `zsh: unknown file attribute: ^-` 에러가 발생하며 명령이 거부됨.
* **원인 가설:** macOS의 기본 쉘인 Zsh는 대화형(Interactive) 모드에서 `#`를 주석 기호로 인식하지 않고, 확장 경로(Globbing) 속성으로 해석하려다 충돌이 발생했을 것으로 추정함.
* **해결 및 대안:** 터미널에 `setopt interactivecomments` 옵션을 적용하여 Zsh가 `#` 이후의 문자열을 정상적인 주석으로 무시하도록 처리하여 에러를 해결함.

**[트러블슈팅 2] Docker 컨테이너 포트 충돌(Port Already Allocated) 에러 방어**
* **문제 상황:** 포트 매핑 실습 시 호스트의 특정 포트(예: 8080)를 열려고 할 때, 이미 다른 프로세스가 해당 포트를 점유하고 있으면 컨테이너 실행이 실패함.
* **원인 가설:** 물리적인 1대의 로컬 컴퓨터(호스트)에서는 동일한 포트 번호를 2개의 프로그램이 동시에 나눠 쓸 수 없기 때문(Port Collision).
* **해결 및 대안:** `docker ps -a` 명령어로 기존에 떠 있는 컨테이너의 포트를 점검하고, 겹치는 경우 `docker rm -f 컨테이너명`으로 삭제하거나, `docker run -p 8081:80`처럼 호스트 측 포트 번호를 우회하여 매핑하는 방식으로 논리적 충돌을 방어함.

## 6. 학습 개념 정리

**1) 절대 경로와 상대 경로의 차이**
* **절대 경로:** 파일 시스템의 최상위 루트(`/`) 또는 홈 디렉토리(`~`)를 시작점으로 하는 전체 경로. 작업 위치와 무관하게 고정된 대상을 가리킴. (예: `/Users/user/workspace/src`)
* **상대 경로:** 현재 작업 중인 디렉토리(PWD)를 기준으로 한 경로. 위치 이동 시 대상이 변경됨. `.`(현재 위치), `..`(상위 폴더) 사용함. (예: `../test_dir/data.txt`)

**2) 파일 권한(r/w/x)과 8진수(Octal) 표기법**
권한은 소유자, 그룹, 기타 사용자로 나뉘며, 2진수 비트 연산을 기반으로 한 8진수(0~7)로 압축 표기함. (읽기 r=4, 쓰기 w=2, 실행 x=1)
* **644 (`-rw-r--r--`):** 6(4+2), 4, 4 조합. 소유자만 읽기/쓰기가 가능하며 나머지는 읽기 전용. (일반 텍스트 파일 표준)
* **755 (`drwxr-xr-x`):** 7(4+2+1), 5(4+1), 5(4+1) 조합. 소유자는 모든 권한을 가지며 나머지는 읽기 및 실행(디렉토리 진입)만 가능. (디렉토리 및 실행 파일 표준)

**3) 기존 Dockerfile 기반 "커스텀 이미지" 제작**
`FROM`으로 검증된 베이스 이미지(예: `nginx:alpine`)를 불러온 뒤, `COPY`로 로컬 파일을 덮어씌우거나 `RUN`으로 환경을 변형하여, 프로젝트 목적에 맞는 독립적인 실행 도면(Image)을 구축하는 과정.

**4) 포트 매핑의 필요성**
컨테이너는 호스트와 격리된 독립 가상 네트워크를 사용함. 외부에서 컨테이너 내부 프로세스(웹 서버 등)에 접근하려면, 호스트의 특정 포트와 컨테이너의 포트를 연결(포워딩)하는 `-p` (Publish) 설정이 필수적임.

**5) Docker 볼륨 (데이터 영속성)**
컨테이너 삭제 시 내부 데이터도 함께 휘발(Ephemeral)되는 문제를 해결하기 위해, 호스트의 특정 디렉토리나 도커 엔진이 관리하는 스토리지 영역을 컨테이너에 마운트하여 데이터를 영구 보존하는 기술.

**6) Git과 GitHub의 역할 차이**
* **Git:** 로컬 환경에서 소스 코드의 버전 이력을 추적하고 관리하는 분산형 버전 관리 시스템(VCS).
* **GitHub:** Git 로컬 저장소를 클라우드에 업로드하여 백업하고, 원격 협업(Pull Request 등)을 지원하는 소스코드 호스팅 플랫폼.
