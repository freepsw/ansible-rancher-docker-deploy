# Create  and load the docker image for data api using ansible (version 2.3)

## PART 1. Ansible roles for creating data api image

### 전체 작업 프로세스 설명
1. create_docker_env
 - 대상서버 : all hosts
 - web service(data-api)용 docker image를 생성하기 위해 필요한 파일을 복사

2. build-docker-image
 - 대상서버 : Rancher-Server
 - FTP 서버를 통해 web service 실행에 필요한 파일을 다운받는다.
 - 압축을 해제하고, 필요한 설정(hostname, jdbc, hdfs ...)값을 변경한다.
 - Dockerfile을 이용하여 docker image를 build한다. (docker image는 한곳에서만 build)
 - Build된 docker image를 save하여 압축파일로 저장한다.
 - Save된 docker image 파일을 모든 host에 복사한다.  
   (scp를 이용한 전송, 사전에 ssh연결이 되어 있어야 함. 00.system > ssh관련 role 활용)
 -

3. load_docker_image
 - 대상서버 : "Agents" (Rancher-Server를 제외한 host, rancher-server에는 app를 배포하지 않음)
 - "build-docker-image" role에서 복사된 압축파일을 이용하여 docker image를 load한다.
 - docker-hub에 image가 없어도 docker 실행이 가능해짐

### ansible-playbook code

```yml
- name: Build docker image using data-api tar file
  hosts: all
  vars_files:
    - inventories/group_vars/main.yml
  roles:
    - role: create_docker_env

- name: Build docker image using data-api tar file
  hosts: "Rancher-Server"
  vars_files:
    - inventories/group_vars/main.yml
  roles:
      - build-docker-image

- name: Load docker image from archived file
  hosts: "Agents"
  vars_files:
    - inventories/group_vars/main.yml
  roles:
      - load_docker_image
```

### Run all process using ansible-playbook
```
- ansible-playbook -i inventories/staging/hosts -v main.yml
```


## PART 2. Test deployed docker image

### mariadb 구동 및 테스트
- 테스트할 항목은 정상적으로 dump된 db를 import하였는지 확인한다.
- 확인할 DB = wfs, user = wfs, pw = wfs
```
> docker run -v /home/rts/apps/docker/dump:/docker-entrypoint-initdb.d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='!mysql00' --name mariadb-test -d mariadb:10.1
```

- docker run 이후에 docker exec로 접속하여 아래의 mysql 접속(wfs계정) 후, script 실행 (mariadb table alter)
```
ALTER TABLE WFS_NODE ADD (owner VARCHAR(255));
ALTER TABLE WFS_NODE_GROUP ADD (owner VARCHAR(255));
ALTER TABLE WFS_WORKFLOW ADD (owner VARCHAR(255));
ALTER TABLE WFS_SCHEDULE ADD (owner VARCHAR(255));
ALTER TABLE WFS_WORKFLOW_JOB ADD (owner VARCHAR(255));
ALTER TABLE WFS_SCHEDULE_JOB ADD (owner VARCHAR(255));
```


### web service 구동 및 테스트
- 테스트할 항목은 browser를 통한 rest 요청시 정상 응답 확인
```
> docker run -ti -p 7070:7070 --link mariadb-test dpcore/data-api:v0.0.1
```
- Web browser에서 아래의 주소 입력
```
> curl http://<ip>:7070/api/v1/workflow?requestUser=dpcore
> http://localhost:7070/api/v1/common/webhdfs/liststatus
> http://<ip>:7070/api/v1/common/webhdfs/liststatus?webhdfsUrl=http://<ip>:50070/webhdfs/v1/user/
```


###  docker run 시점에 "/etc/hosts"에 host를 추가할 경우 (--add-host hosname:ip)
- container link가 아닌, 외부 시스템에 hostname으로 접근할 필요가 있을때,
- container os의 /etc/hosts 파일에 등록하기 위함.
```
> docker run -ti --add-host=dataplatform03:10.178.50.113 --add-host=dataplatform03.test.com:10.178.50.113 -p 7070:7070 --link mariadb-test dpcore/data-api:v0.0.1
```



## ETC
### docker 명령어 정리
```
- save & load
> docker save core/verticle | gzip > core-verticle.tar.gz
> zcat core-verticle.tar.gz | docker load
```


- clean up docker
```
> docker stop $(docker ps -q)
> docker rm $(docker ps -a -q)
> docker rmi $(docker images -q)
> docker volume rm $(docker volume ls -q)
```
