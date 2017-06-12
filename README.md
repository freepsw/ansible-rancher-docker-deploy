# Automate the deploy process using ansible and orchestrate the docker containers using rancher
- perfect harmony of docker + rancher + ansible
- 웹기반 서비스의 배포 및 운영을 운영자의 개입을 최소화할 수 있도록 자동화.

### [시스템 요건]
-  개발된 웹서비스를 클라우드 환경(Open Stack 등)으로 쉽게 배포할 수 있고,
-  Amazone Web Service의 EMR과 같은 Hadoop Cluster를 빠르게 생성/삭제할 수 있어야 하고,
-  배포된 웹 서비스의 Scale out / Fail Over / Load balancing … 등 운영을 위한 설정이 쉬워야 하며,
-  Hadoop Eco 소프트웨어(Apache spark, Apache Kafka, Elasticsearch …)를 빠르게 설치 운영할 수 있어야 하고,
-  이 모든 것이 자동화 될 수 있는 API기반으로 구현되어야 한다.

##### 오픈소스 활용 방안
![service architecture](https://github.com/freepsw/ansible-rancher-docker-deploy/blob/master/10.img/img_1.png)
- Ansible로 필요한 서버설정을 자동화하고,
- Dokcer & Rancher를 이용하여 서비스의 배포 및 운영을 효율적으로 수행한다.


### [오픈소스의 역할]
#### Ansible 2.3.0
- 웹서비스 실행에 필요한 설치파일 및 데이터 복사 (모든 서버))
- 웹서비스가 컨테이너 기반으로 동작할 수 있는 환경 구성
  - ssh connection without password
  - install docker & docker compose
  - install rancher server & hosts
  - create container image for web service
- ansible-playbook의 role 기반으로 작업을 분류하여 모듈화

#### Docker 1.12.4
- 개발된 웹서비스를 container로 생성하여, 운영할 수 있는 환경 제공
- Rancher 서버에서 docker를 이용하여 container 관리
- kubernetes engine을 지원하기 위하여 1.12.5 docker engine 설치

#### Rancher 1.5.6
- Container orchestration engine
- Web UI기반으로 container를 쉽게 설치 & 확장 & 로드밸런싱 가능
- Container에 대한 fail-over 및 자원모니터링 제공
- Hadoop Eco 등 다양한 오픈소스 배포를 위한 Catalog 제공
- 배포관리 자동화를 위한 Open API를 제공하여, 전체 프로세스 자동화 가능

#### 전체 구성도
- Web Service Cluster를 rancher stack을 생성하여 구성하고,
- Hadoop Cluster를 또다른 rancher stack으로 분리하여 구성하고,
- 상호간의 네트웍을 연결한다. (이때 Hadoop NameNode IP를 API로 조회해서 자동으로 설정해야함.)
![img-1](https://github.com/freepsw/ansible-rancher-docker-deploy/blob/master/10.img/Img_4.png)


## STEP 1. Install the necessary software using ansible-playbook (Docker, Rancher)
- Ansible로 rancher cluster 구성에 필요한 설치를 자동화한다.
- 웹서비스 배포에 필요한 파일을 각 서버에 복사하고,
- 이를 이용하여 웹서비스용 docker container image를 build한다.
 - 만약 docker-hub를 이용할 수 있는 환경이라면, 사전에 docker image를 등록한다.
![img-2](https://github.com/freepsw/ansible-rancher-docker-deploy/blob/master/10.img/Img_2.png)

### 1. Set ssh connection
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/00.system

#### - ansible manager 와 host서버들간 ssh 연결
  - inventory 파일에 ansible_ssh_pass를 이용하여 미리 패스워드 설정
  - ansible guide에서는 비밀번호를 평문으로 관리하지 말고, vault를 이용하라고 가이드함.

####  - rancher server와 rancher host간 ssh 연결
  - rancher server에 생성된 docker image를 rancher host에 복사하는 용도

### 2. Install & run docker & rancher server using ansible-playbook
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/01.rancher

- docker install & run
- rancher server install & run
- register rancher hosts to rancher server


### 3. Create and load the docker image for web-service
- A https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/02.data_api

- change configuration setting according to server environments
- create docker image using web service install file at rancher master
- copy created image file to rancher host and load copied image to docker


## STEP 2. Deploy service using rancher api
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/02.service_deploy
- curl command를 이용하여, web service 배포
- curl command를 이용하여, hadoop cluster 배포

![img-3](https://github.com/freepsw/ansible-rancher-docker-deploy/blob/master/10.img/Img_3.png)
