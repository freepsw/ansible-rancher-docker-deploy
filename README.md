# Deploying docker container using rancher and automating the deploy process using ansible-playbook
- perfect harmony of docker + rancher + ansible

## PART 1. Install the necessary software (Docker, Rancher)

### 1. Set ssh connection
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/00.system
#### - ansible manager 와 host서버들간 ssh 연결
- inventory 파일에 ansible_ssh_pass를 이용하여 미리 패스워드 설정
- ansible guide에서는 비밀번호를 평문으로 관리하지 말고, vault를 이용하라고 가이드함.

#### - rancher server와 rancher host간 ssh 연결
- rancher server에 생성된 docker image를 rancher host에 복사하는 용도

### 2. Install & run docker & rancher server
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/01.rancher
#### - docker instll & run

#### - rancher server install & run

#### - register rancher hosts to rancher server


### 3. Create and load the docker image for data-api
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/02.data_api


## PART 2. Deploy service using rancher api
- https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/02.service_deploy
- curl command를 이용하여, data-api service 배포
- curl command를 이용하여, hadoop cluster 배포
