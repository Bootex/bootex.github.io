---
layout: post
published: True
title: How to use Docker?
categories: Server
tags:
- Linux
- Environment
---

Virtual Container로 유명한 도커의 특징과 실제로 환경 구축한 내용을 정리해보았다.



- Ubuntu 18.04 LTS 64bits



<!--more-->

## What is docker?

#### Docker is the world’s leading software containerization platform

- 복잡한 리눅스 어플리케이션을 컨테이너로 묶어서 실행 가능.
- 개발 테스트, 서비스 환경을 하나로 통일하여 효율적으로 관리 가능.
- 컨테이너(이미지)를 전 세계 사람들과 공유
- 리눅스 커널에서 제공하는 컨테이너 기술을 이용
- Github와 비슷한 방식으로 Docker Hub 제공

Container
: 가상화보다 좀 더 가벼운 기능 제공

가상화
: PC내에서 가상의 PC를 만드는 것을 의미.
  가상 머신에 각종 서버 프로그램, DB등을 설치하여 애플리케이션이나 웹사이트를 실행. ex) VMware, Virtual Box

클라우드 서비스
: 미리 구축한 가상 머신 이미지를 여러 서버에 복사하여 실행하면, 이미지 하나로 서버를 계속 생산 가능.
  이러한 가상화 기술을 이용하여 서버를 임대해주는 서비스.

반가상화
: 호스트와 커널을 공유하는 기술.




### Why do we use docker?
가상 머신의 성능 문제가 있다 보니, 리눅스 컨테이너가 탄생하였다
컨테이너 안에 가상 공간을 만들지만, 실행 파일을 호스트에서 직접 실행된다.**(가상 머신에서의 성능 저하 문제를 해결)**

리눅스 커널의 cgroups와 namespaces가 제공하는 기술을 이용하여 구현되었다.



## Docker feature
**도커는 Guest OS를 설치하지 않음**
- 이미지에 서버 운영을 위한 프로그램과 라이브러리만 격리해서 설치
- 이미지 용량을 줄일 수 있음.
- 호스트와 OS 자원(System call)을 공유 가능



**도커는 하드웨어 가상화 계층이 없음**
- 메모리 접근, 파일 시스템, 네트워크 전송 속도가 가상 머신에 비해 빠름.
- 호스트와 도커 컨테이너 사이의 성능 차이가 크지 않음.
- 이미지 생성과 배포에 특화
- 이미지 버전 관리 및 중앙 github와 같이 저장소에서 이미지를 올리고 받을 수 있음(push,pull)
- 다양한 API를 제공하여 원하는 만큼 자동화 가능, 개발과 서버 운영에 매우 유용



**이미지는 서비스 운영에 필요한 서버 프로그램, 소스 코드, 컴파일된 실행 파일을 묶은 형태**
**컨테이너는 이미지를 실행한 상태**

- 저장소에 이미지를 올리고 받음(Push/Pull)
- 하나의 이미지로 여러 개의 컨테이너를 만들 수 있음



**도커는 이미지의 바뀐 부분을 유니온 파일 시스템 형식(aufs, btrfs, device mapper)을 통해 관리**

- 도커는 베이스 이미지에서 **바뀐 부분만 이미지로 생성**
- 컨테이너로 실행할 때는 베이스 이미지와 **바뀐 부분을 합쳐서 실행**
- Docker Hub 및 개인 저장소에서 이미지를 공유할 때 **바뀐 부분만 주고 받음**
- 각 이미지 간의 **의존 관계 형성**



### Docker server Environment
- 기존에는 물리적으로 구축하던 서버를 최근에는 클라우드 환경에서 이용.
- 하지만, 서버 대수가 증가함에 따라 사람이 일일이 세팅하기에 어려움이 많음.



### Immutable Infrastructure
- Host OS와 서비스 운영 환경(서버 프로그램, 소스 코드, 컴파일된 바이너리)을 분리
- 한번 설정한 운영 환경은 **변경하지 않는다(Immutable)**
- 서비스 운영 환경을 이미지로 생성한 뒤, 서버에 배포하여 실행
- 서비스가 업데이트되면 운영 환경 자체를 변경하지 않고, **이미지를 생성하여 배포**
- 클라우드 플랫폼에서 서버를 쓰고 버리는 것과 같이 Immutable Infrastructure도 **서비스 운영 환경 이미지를 한 번 쓰고 버림**



### Immutable Infrastructure's benefit
1. 편리한 관리
  - 서비스 환경 **이미지만 관리**
  - 중앙 관리를 통한 **체계적인 배포와 관리**
  - 이미지 생성에 **버전 관리 시스템 활용**
2. 확장
  - 이미지 하나로 서버를 계속 찍어낼 수 있음
  - 클라우드 플랫폼의 자동 확장(Auto scaling)기능과 연동하여 손쉽게 서비스 확장
3. 테스트
  - 개발자 PC, 테스트 서버에서 이미지를 실행만 하면 서비스 이 운영 환경과 동일한 환경이 구성됨
  - 테스트가 간편
4. 가벼움
  - 운영체제와 서비스 환경을 분리하여 가볍고(Lightweight) 어디서든 실행가능한(Portable) 환경 제공



## Docker Installation

설치가이드는 공식 페이지의 설치 가이드와 동일하다.



1. Remove older versions

```bash
sudo apt-get remove docker docker-engine docker.io
```



2. Set up the repository

   1. update apt package index

   2. install package to allow `apt` to use a repository over HTTPS

      ```bash
      sudo apt-get install \
          apt-transport-https \
          ca-certificates \
          curl \
          software-properties-common
      ```

      

   3. Add Docker's Official GPG key

      ```bash
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
      ```

   4. Verify fingerprint

      ```bash
      sudo apt-key fingerprint 0EBFCD88
      ```

      

   5. Install Docker

      ```bash
      sudo add-apt-repository \
         "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
         $(lsb_release -cs) \
         stable"
      ```

3. Install Docker CE(Community Edition)

   1. Update apt package index

      ```bash
      sudo apt-get update
      ```

      

   2. Install latest version of docker CE

      ```bash
      sudo apt-get install docker-ce
      ```

      

4. (Optional) run docker without sudo(현재 유저 도커 사용자에 추가)

   ```bash
   sudo gpasswd -a $USER docker
   ```

5. reboot



재부팅 후, 해당 설정이 반영된다.

```bash
docker run hello-world
```

