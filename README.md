<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 README.md 파일에 작성하여 PR을 보내주세요.

### 0단계 - pem 키 생성하기

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)에 업로드해주세요
2. 업로드한 pem키는 무엇인가요.
   - [KEY-shinsunyoung.pem](https://drive.google.com/file/d/1pTuqruDLUuQYWRaJXAldOBBbG_9GuYgM/view)

### 1단계 - 망 구성하기
1. 구성한 망의 서브넷 대역을 알려주세요
- 대역 : 
  - shinsunyoung-public-a / 192.169.1.0/26
  - shinsunyoung-public-c / 192.169.1.64/26
  - shinsunyoung-internal-a / 192.169.1.128/27
  - shinsunyoung-admin-c / 192.169.1.160/27

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL :  http://shinsunyoung.p-e.kr:8080/



---

### 2단계 - 배포하기
1. TLS가 적용된 URL을 알려주세요

- URL : https://shinsunyoung.p-e.kr

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.
```bash
#!/bin/bash

## 변수 설정
EXECUTION_PATH=$(pwd)
SHELL_SCRIPT_PATH=$(dirname $0)
BRANCH=$1
PROFILE=$2
PROJECT_PATH=/home/ubuntu/nextstep/infra-subway-deploy

txtrst='\033[1;37m' # White
txtred='\033[1;31m' # Red
txtylw='\033[1;33m' # Yellow
txtpur='\033[1;35m' # Purple
txtgrn='\033[1;32m' # Green
txtgra='\033[1;30m' # Gray



function pull() {
  echo -e "${txtylw}=======================================${txtrst}"
  echo -e ">> Pull Request ${BRANCH} 🏃"
  git pull origin $BRANCH
  echo -e "${txtylw}=======================================${txtrst}"
}


function build() {
  echo -e "${txtylw}=======================================${txtrst}"
  echo -e ">> Gradle clean build 🏃"
  ./gradlew clean build
  echo -e "${txtylw}=======================================${txtrst}"
}


function kill_running_java_process() {
  echo -e "${txtylw}=======================================${txtrst}"
  CURRENT_PID=$(pgrep -f subway*.jar)

  if [[ -z "${CURRENT_PID}" ]]
          then
          echo ">> 실행 중인 애플리케이션이 없습니다."
  else
          echo -e ">> 실행 중인 애플리케이션의 PID(${CURRENT_PID})를 종료합니다. 🏃"
          echo "kill -15 ${CURRENT_PID}"
          sudo kill -15 ${CURRENT_PID}
          sleep 5
  fi
  echo -e "${txtylw}=======================================${txtrst}"
}


function deploy() {
  echo -e "${txtylw}=======================================${txtrst}"
  JAR_NAME=$(basename -- build/libs/*.jar)
  echo -e ">> Deploy ${JAR_NAME}🏃"

  nohup java -jar -Dspring.profiles.active=$PROFILE $JARFILE 1>log.txt 2>&1 &
  echo -e "${txtylw}=======================================${txtrst}"
}


## 조건 설정
if [[ $# -ne 2 ]]
then
  echo -e "${txtylw}=======================================${txtrst}"
  echo -e "${txtgrn}  << 스크립트 🧐 >>${txtrst}"
  echo -e ""
  echo -e "${txtgrn} $0 브랜치이름 ${txtred}{ prod | dev }"
  echo -e "${txtylw}=======================================${txtrst}"
  exit
fi

git fetch
master=$(git rev-parse ${BRANCH})
remote=$(git rev-parse origin/${BRANCH})

if [[ $master == $remote ]]; then
  echo -e "[$(date)] Nothing to do !!! 😫"
  exit 0
else
  echo -e "> 브랜치가 변경되었습니다."
  pull
  build
  kill_running_java_process
  deploy
fi

cd ${PROJECT_PATH}
```

