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

1. 서버에 접속을 위한 pem키를 [구글드라이브](https://drive.google.com/drive/folders/1dZiCUwNeH1LMglp8dyTqqsL1b2yBnzd1?usp=sharing)
   업로드해주세요 - 넹

2. 업로드한 pem키는 무엇인가요.

- bastion 서버 접근용 pem 키 : meeiingjae-keypair.pem
- 내부망 접근용 pem 키 : key-meeingjae.pem

### 1단계 - 망 구성하기

1. 구성한 망의 서브넷 대역을 알려주세요

- 대역 : 192.168.99.0/24
    - public 1 : 192.168.99.0 ~ 192.168.99.63 (/26)
    - public 2 : 192.168.99.64 ~ 192.168.99.127 (/26)
    - private 1 : 192.168.99.128 ~ 192.168.99.160 (/27)
    - bastion 1 : 192.168.99.161 ~ 192.168.99.192 (/27)

2. 배포한 서비스의 공인 IP(혹은 URL)를 알려주세요

- URL : www.meeingjae-subway.kro.kr
- IP  : 3.35.131.48:8080

---

### 2단계 - 배포하기

1. TLS가 적용된 URL을 알려주세요

- URL : https://3.35.131.48
- http://www.meeingjae-subway.kro.kr/ 로 접근하면 redirection url이 보입니다 !

### 요구사항 설명

* **운영환경 구성하기**
    * 웹 어플리케이션 앞단에 Reverse Proxy 구성하기
        * 외부망에 Nginx로 Reverse Proxy를 구성
        * Reverse Proxy에 TLS 설정
    * 운영 데이터베이스 구성
* **개발 환경 구성하기**
    * 설정 파일 나누기
        * JUnit : h2
        * Local : docker(mysql)
        * Prod : docker(mysql)

### nginx

- 디렉터리
    - ~/nginx
- 컨테이너
    - name
        - proxy
    - port
        - 80
        - 443
- nginx.conf 경로
    - ~/nginx/nginx.conf

### subway-service

- 디렉터리
    - ~/infra-subway-deploy

- 설정파일 (proerties)
    - application-prod.properties
        - mysql 3306 port
    - application-local.properties
        - mysql 13306 port
    - application-test.properties
        - h2

### subway-db

- Docker Image
    - meeingjae/subway-mysql:0.0.1
- Dockerfile 경로
    - ~/infra-subway-deploy/Dockerfile
- scheam 경로
    - ~/infra-subway-deploy/schema.sql
- 컨테이너 (name)
    - subway-db-prod
        - port : 3306
    - subway-db-local
        - port : 13306

---

### 3단계 - 배포 스크립트 작성하기

1. 작성한 배포 스크립트를 공유해주세요.

###

# Subnet

### 192.168.99.0/24

# Key Pair

* key-meeingjae

# EC2

## meeingjae-public-1 (외부망 public 서버)

* instance
    * t3.medium
* subnet
    * 192.168.99.0 ~ 192.168.99.63 (/26)
* public ipv4 address
    * 3.35.131.48
* AZ
    * ap-2a
* Storage
    * 16 GB
* 방화벽(보안 그룹)
    * meeingjae-public-1
        * inound
            * ssh - bastion 서브넷 허용
            * tcp - 8080 포트 허용
            * icmp ipv4 - 모두

## meeingjae-public-2 (외부망 public 서버2)

* instance
    * t3.medium
* subnet
    * 192.168.99.64 ~ 192.168.99.127 (/26)
* public ipv4 address
    * 3.38.207.186
* AZ
    * ap-2b
* Storage
    * 16 GB
* 방화벽(보안 그룹)
    * meeingjae-public-2
        * inound
            * ssh - bastion 서브넷 허용
            * tcp - 8080 포트 허용
            * icmp ipv4 - 모두

## meeingjae-private-1 (내부망 private 서버)

* instance
    * t3.medium
* subnet
    * 192.168.99.128 ~ 192.168.99.160 (/27)
* public ipv4 address
    * 15.164.173.185
* AZ
    * ap-2b
* Storage
    * 8 GB
* 방화벽(보안 그룹)
    * meeingjae-private-1
        * inound
            * ssh - bastion 서브넷 허용
            * tcp - 3306 포트 허용
            * icmp ipv4 - 모두

## meeingjae-bastion-1 (bastion 관리서버)

* instance
    * t3.medium
* subnet
    * 192.168.99.161 ~ 192.168.99.192 (/27)
* public ipv4 address
    * 15.165.59.84
* AZ
    * ap-2b
* Storage
    * 8 GB
* 방화벽(보안 그룹)
    * meeingjae-bastion-1
        * inound
            * ssh - 내 ip
            * icmp ipv4 - 모두

# Internet Gateway

* name
    * meeingjae-internet-gateway

## routing table

* 0.0.0.0 -> meeingjae-internet-gateway

