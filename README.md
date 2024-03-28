# 쉬운 도커

[1. 가상화 기술](#1-가상화-기술)

[2. 이미지와 컨테이너](#이미지와-컨테이너)

[참고 - 명령어 정리(Window)](#명령어-정리)

## 1. 가상화 기술

### 1.1 서버 운영 방식

* 베어메탈(Baremetal)
* 하이버파이저(Hypervisor)
* 컨테이너(Container)

### 1.2 가상화

물리적인 환경에서 논리적인 컴퓨팅 환경을 만든다.

한 대의 컴퓨터에서 여러대의 가상 환경을 만들어서 안정성을 높인다.

각 가상환경(가상 OS)는 사용자가 직접 리소스를 분배할 수 있다.(제한을 걸 수 있다.)

#### 1.2.1 하이버파이저

가상 OS를 만들어서 사용한다.

호스트 OS에서 여러 개의 게스트 OS를 실행한다.

각 프로세스는 OS의 커널에게 시스템 콜을 통해 리소스를 요청한다.

다른 OS끼리는 시스템 콜 명령어가 다르지만, 하이버파이저가 각 커널에 맞도록 번역해준다.

#### 1.2.2 컨테이너

하이퍼바이저 가상화 방식에 비해서 가볍고 빠르다.

리눅스의 LXC를 활용, 리눅스 커널 자체에서 제공하는 격리 기술을 사용한다.

LXC를 통해 만들어진 각 공간을 컨테이너라고 호칭한다.

모든 컨테이너는 호스트 OS의 커널을 공유한다.(하나의 커널을 공유 -> 번역이 필요없다.)

하이퍼바이저 방식에 비해서 오버헤드가 더 적고, 리소스를 효율적으로 사용한다.

#### 1.2.3 도커

컨테이너 플랫폼 : 컨테이너 가상화 도구

컨테이너 엔진, 컨테이너 런타임으로 구성되어 있다.
* 런타임 : 사용자의 요청을 받아서 컨테이너를 관리하는 역할(도커에선 runc 사용)
* 엔진 : 커널과 통신하며 격리된 공간을 만드는 역할 

클라이언트에서 명령을 전달하고, 서버(도커 데몬)에서 결과를 반환

Docker CLI(Command Line Interface)가 명령어를 받아서 API로 변환하여 도커 데몬으로 전달한다.

도커 멍령어는 docker (Management Command) (Command) 로 구성되어 있다.

Management Command는 생략이 가능해서 보통은 docker (Command)의 형태로 사용한다.

Management Command와 Command의 뒤에 --help를 붙여서 매뉴얼을 확인할 수 있다.

## 2. 이미지와 컨테이너

### 2.1 이미지

서비스 실행을 위해서는 Config가 필요하다.

Config : OS, 실행에 필요한 설정(의존 요소), 실행파일/소프트웨어(프로그램)

도커 이미지에는 모든 요소들이 압축되어 준비되기 때문에 기타 자료들이 필요없다.

이미지는 특정 시점의 파일 시스템(디렉토리)를 저장한 압축 파일이라고 생각하면 된다.

컨테이너를 실행할 때 이미지 안에 있는 것들이 같이 실행된다.

### 2.2 이미지와 컨테이너

프로그램 : 디스크에 저장된 실행 가능한 소프트웨어

프로세스 : 프로그램이 실행된 상태로 리소스를 사용한다.

이미지와 컨테이너는 프로그램과 프로세스의 관계와 유사하다.

이미지는 저장공간을 차지하는 실행 가능한 소프트웨어, 이미지를 실행하면 컨테이너가 된다.

### 2.3 메타데이터

데이터에 대한 데이터

이미지가 압축된 데이터라면 메타데이터는 이미지에 대한 정보를 기술한 데이터이다.

도커 이미지의 메타데이터는 env와 cmd 두가지가 존재한다.

보통 이미지 디버깅을 위해 메타데이터를 수정해서 실행한다.

#### 2.3.1 cmd

cmd에 적힌 명령어를 통해 어떻게 실행할지 결정한다.

Cmd 변경 실행 예시
```shell
docker run --name customCmd nginx cmd (원하는 custom 명령)
```
이때 변경되는 것은 컨테이너의 cmd라서 이미지의 cmd는 그대로 유지된다.

#### 2.3.2 -d 옵션

-d 옵션 : 백그라운드 실행 옵션

-d 옵션을 주지 않으면 프로세스가 종료될 때 까지 화면에 로그가 출력된다.

#### 2.3.3 env

--env 옵션을 통해 컨테이너 실행시 env를 설정할 수 있다.

env또한 key=value 형식으로 입력해야 한다.

### 2.4 컨테이너의 라이프사이클

docker run 명령은 run과 create가 합쳐진 명령이라 컨테이너의 생성과 실행이 동시에 진행된다.

docker restart를 써서 프로세스를 재시작 할 수 있다.(10초 뒤 재시작)

docker pause는 현재 컨테이너를 일시 중지시킨다.(메모리만 사용)

docker unpause를 통해 pause 시점부터 다시 실행시킬 수 있다.

docker stop은 컨테이너를 종료하고, 해당 컨테이너를 재실행 하면 처음부터 실행된다.

docker rm, 컨테이너 삭제 명령은 모든 상태에서 가능하다.(-f 강제 종료)

-i : 컨테이너를 실행하면서 해당 컨테이너의 출력을 터미널에 띄워줌

## 3. 이미지 레지스트리

### 3.1 이미지 레지스트리

도커 이미지를 저장하기 위한 저장소

도커 허브 : 대표적인 도커 이미지 퍼블릭 레지스트리

#### 기능
1. 이미지 다운로드/업로드
2. 이미지 검색
3. 이미지 버전 관리
4. 이미지 보안 검증
5. 파이프라인 기능과 연계해서 자동 배포 및 알림 제공

docker run으로 실행시 로컬 스토리지에 이미지가 존재하는지 확인하고, 없다면 온라인 레지스트리에서 다운받아 사용한다.

보안을 위해 도커 허브와 같은 퍼블릭 레지스트리 대신 직접 레지스트리를 만들 수 있다.(프라이빗 레지스트리)

#### 이미지 네이밍 규칙

이미지 이름은 레지스트리주소/프로젝트명/이미지명:이미지태그로 구성된다.

레지스트리 주소의 기본 값은 도커 허브의 주소인 docker.io 이다.

다른 레지스트리에서 이미지를 받고 싶다면 가장 앞에 해당 레지스트리의 주소를 표기해야 한다.

도커 허브를 사용할 경우 가입한 사용자명이 프로젝트 명이 된다.

이미지 태그는 받고자 하는 이미지의 버전으로 숫자/영문 모두 가능하다.

### 3.2 이미지 레지스트리 실습

도커 허브 주소 : hub.docker.com

내가 push한 이미지는 Repositories에 저장된다.

```shell
# 로컬로 이미지 다운로드
docker pull (이미지명)

# 로컬 스토리지의 이미지명 추가(기존 이미지는 그대로 있고, 새 이름의 이미지를 새로 생성)
docker tag (기존 이미지명) (추가할 이미지명)

# 원격 레지스트리에 이미지 업로드
docker push (이미지명)
```

이미지 업로드시에는 업로드할 계정의 인증 정보가 필요하다.

```shell
# 레지스트리 인증 정보 생성
docker login

# 인증 정보 삭제
docker logout
```

## 명령어 정리(Window)

```shell
# 현재 실행중인 모든 컨테이너 삭제
docker ps -aq | ForEach-Object {docker rm -f $_}

# 현재 도커 클라이언트/서버 버전 확인
docker version

# 현재 도커 정보 확인 
docker info

# 컨테이너 실행
docker run (옵션들) (이미지이름)

# 컨테이너 삭제(실행중인 컨테이너는 -f 옵션 사용)
docker rm 컨테이너 이름/id

# 이미지 조회 - 이미지 이름을 붙이면 해당 이미지만 조회할 수 있다.
docker images 또는 image ls (이미지이름)

# 이미지 실행 (-d는 백그라운드 실행 옵션)
docker run -d --name {컨테이너명} 이미지명

# 이미지 세부정보 조회
docker image inspect 이미지명

# 컨테이너 세부정보 조회
docker container inspect 컨테이너명

# 컨테이너 실행시 메타데이터의 cmd 덮어쓰기
docker run 이미지명 (명령)

# 컨테이너 실행시 메타데이터의 env 덮어쓰기
docker run --env 이미지명 KEY=VALUE

# 실행중인 컨테이너의 로그 확인(-f 옵션을 붙이면 로그를 지속적으로 확인)
docker log (컨테이너 이름)

# 로컬로 이미지 다운로드
docker pull (이미지명)

# 로컬 스토리지의 이미지명 추가(기존 이미지는 그대로 있고, 새 이름의 이미지를 새로 생성)
docker tag (기존 이미지명) (추가할 이미지명)

# 원격 레지스트리에 이미지 업로드
docker push (이미지명)

# 레지스트리 인증 정보 생성
docker login

# 인증 정보 삭제
docker logout
```