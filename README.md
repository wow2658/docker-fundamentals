# AI/SW 개발 워크스테이션 구축 결과 보고서 작성 중간저장01

## 1. 프로젝트 개요
- 목표: 코드가 특정 로컬 환경에서만 동작하는 문제를 방지하고, 팀원 누구나 동일하게 재현 가능한 실행 환경 구축.

- 핵심 기술: 리눅스 CLI 기반 권한 제어, Docker를 활용한 컨테이너 격리 및 환경 코드화(Dockerfile), Git/GitHub를 통한 버전 관리 및 협업 환경 구성.

## 2. 실행 환경
- **OS:** macOS (OrbStack 환경)
- **Shell:** zsh
- **Docker:** 28.5.2
- **Git:** (터미널에서 `git --version` 확인 후 숫자 기입)

## 3. 수행 항목 체크리스트 및 검증 증거

평가자가 각 수행 항목의 달성 여부와 증거를 한눈에 파악할 수 있도록 체크리스트와 로그를 통합하여 작성했습니다.

### [x] 3.1 터미널 기본 조작 및 폴더 구성
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


### [x] 3.2 권한 변경 실습
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

### [x] 3.3 Docker 설치 및 기본 점검
* **검증 방법:** `docker --version` 및 `docker info` 명령어를 통해 데몬 정상 동작 여부 확인.
* **수행 로그:**

```bash
# 1. 도커 버전 확인
docker --version

# 2. 도커 데몬 정상 동작 점검 (시스템 정보 출력)
docker info
```
<img width="800" height="1626" alt="스크린샷_2026-04-02_오후_8 35 04" src="https://github.com/user-attachments/assets/3baf19b9-0ea7-4c90-b7cb-7f72d2a51ad9" />


### [x] 3.4 hello-world 및 ubuntu 컨테이너 실행 (기본 운영 명령)
* **검증 방법:** `hello-world` 이미지 실행으로 기본 동작 검증, `ubuntu` 컨테이너 내부 진입 후 명령어 실행 및 `docker ps` 상태 확인.
* **수행 로그:**
```bash
docker run hello-world

docker run -it -d --name test-attach ubuntu:22.04 bash

docker run -it -d --name test-exec ubuntu:22.04 bash

docker ps -a

docker attach test-attach
exit

docker exec -it test-exec bash
exit
```
<img width="2038" height="2126" alt="스크린샷 2026-04-02 오후 9 24 29" src="https://github.com/user-attachments/assets/54493dbe-ea7e-4355-82d7-0faf20f562b7" />

### [x] 3.5 기존 Dockerfile 기반 커스텀 이미지 제작
* **검증 방법:** `nginx:alpine` 베이스 이미지에 커스텀 HTML 파일을 복사하는 Dockerfile 작성 후 `docker build` 성공 확인.
* **수행 로그:**
```bash

```
<img width="2234" height="2990" alt="스크린샷 2026-04-02 오후 9 33 34" src="https://github.com/user-attachments/assets/78a2b674-ddfc-4896-8251-cb1e6f392105" />
<img width="2234" height="1508" alt="스크린샷 2026-04-02 오후 9 33 46" src="https://github.com/user-attachments/assets/b2b387bd-fd2f-46d0-814c-069aba4d92ae" />

### [x] 3.6 포트 매핑 및 브라우저 접속 증거
* **검증 방법:** `-p` 옵션으로 호스트와 컨테이너 포트를 매핑하여 백그라운드(`-d`) 실행 후, `curl` 및 브라우저 접속 확인.
* **수행 로그:**
```bash

```
* **브라우저 접속 증거:**
*(여기에 http://localhost:8080 에 접속된 브라우저 화면 스크린샷 캡처본 첨부 - 주소창 포함)*
<img width="800" height="238" alt="image" src="https://github.com/user-attachments/assets/479a52c8-ae1f-418e-a60d-a3be63b41a0a" />
<img width="2058" height="630" alt="image" src="https://github.com/user-attachments/assets/d928cea1-d76d-49fc-bb6e-92a6e6123955" />


### [x] 3.7 바인드 마운트 반영
* **검증 방법:** 호스트의 디렉토리를 컨테이너에 마운트(`-v`)하고, 호스트에서 파일을 수정했을 때 컨테이너에 즉각 반영되는지 확인.
* **수행 로그:**
```bash

```

### [x] 3.8 Docker 볼륨 영속성 검증
* **검증 방법:** Docker 볼륨 생성 후 컨테이너에 연결하여 데이터 쓰기 진행. 컨테이너 삭제 후 새 컨테이너에 동일 볼륨 연결 시 데이터 유지 확인.
* **수행 로그:**
```bash

```

### [x] 3.9 Git 설정 및 GitHub 연동
* **검증 방법:** `git config` 설정 확인 및 VSCode에서 원격 저장소 푸시 완료 확인.
* **수행 로그:**
```bash

```
* **연동 증거:**
*(여기에 GitHub Repository에 코드 및 README가 push된 화면 스크린샷 첨부)*
---

## 4. 트러블슈팅 (2건)

**[트러블슈팅 1] Zsh 대화형 쉘에서의 주석(`#`) 파싱 에러**
* **문제 상황:** 터미널 기본 조작 실습 중, 주석(`# 1. 현재 위치 확인`)이 포함된 명령어를 복사/붙여넣기 하자 `zsh: unknown file attribute: ^-` 에러가 발생하며 명령이 거부됨.
* **원인 가설:** macOS의 기본 쉘인 Zsh는 대화형(Interactive) 모드에서 `#`를 주석 기호로 인식하지 않고, 확장 경로(Globbing) 속성으로 해석하려다 충돌이 발생했을 것으로 추정함.
* **해결 및 대안:** 터미널에 `setopt interactivecomments` 옵션을 적용하여 Zsh가 `#` 이후의 문자열을 정상적인 주석으로 무시하도록 처리하여 에러를 해결함.

**[트러블슈팅 2] Docker 컨테이너 포트 충돌(Port Already Allocated) 에러 방어**
* **문제 상황:** 포트 매핑 실습 시 호스트의 특정 포트(예: 8080)를 열려고 할 때, 이미 다른 프로세스가 해당 포트를 점유하고 있으면 컨테이너 실행이 실패함.
* **원인 가설:** 물리적인 1대의 로컬 컴퓨터(호스트)에서는 동일한 포트 번호를 2개의 프로그램이 동시에 나눠 쓸 수 없기 때문(Port Collision).
* **해결 및 대안:** `docker ps -a` 명령어로 기존에 떠 있는 컨테이너의 포트를 점검하고, 겹치는 경우 `docker rm -f 컨테이너명`으로 삭제하거나, `docker run -p 8081:80`처럼 호스트 측 포트 번호를 우회하여 매핑하는 방식으로 논리적 충돌을 방어함.

## 5. 학습 개념 정리

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
