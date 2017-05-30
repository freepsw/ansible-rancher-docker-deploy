# Deploying docker container using rancher and automating the deploy process using ansible-playbook
- perfect harmony of docker + rancher + ansible

## PART 1. Install the necessary software (Docker, Rancher)

### 1. set ssh connection

#### ansible manager 와 host서버들간 ssh 연결
- inventory 파일에 ansible_ssh_pass를 이용하여 미리 패스워드 설정
- ansible guide에서는 비밀번호를 평문으로 관리하지 말고, vault를 이용하라고 가이드함.

#### rancher server와 rancher host간 ssh 연결
-

### 2. install & run docker & rancher server
 - https://github.com/freepsw/ansible-rancher-docker-deploy/tree/master/01.ansible_install/01.rancher

### 3. register rancher host to rancher server

###


## PART 2. etc
