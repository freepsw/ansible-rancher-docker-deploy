# PART 0. Create docker image for Data-api
## 1. copy application tar file
```
>  copy and unzip application gz file
```

## 2. build dokcer image
### edit configuration
- mariadb url : config-api-server.properties, dataaccess.properties
- webhdfs url : dataaccess.properties

###  build docker image
```
> docker build -t dpcore/data-api:v0.0.1 .

# test docker image (container에 파일이 정상적으로 복사되었는지 확인)
> docker run -ti --entrypoint="/bin/bash"  dpcore/data-api:v0.0.1

```



# PART 1. Run docker using command line
## 0. export & import docker image (또는 docker build 진행)
- save & load
```
> docker save core/verticle | gzip > core-verticle.tar.gz
> zcat core-verticle.tar.gz | docker load
```

- 아래 방식은 사용하지 않음
```
# export
> docker export 1566c55a9cc0  | gzip >  verticle.tar.gz
> docker export e93da975a15b  | gzip >  mariadb-10-1.tar.gz

# import
> zcat mariadb-10-1.tar.gz | docker import - mariadb:10.1
> zcat core-verticle.tar.gz | docker import - core/verticle
```

## 1. Run mariadb

```
docker run -v /home/rts/apps/docker/datapi/dump:/docker-entrypoint-initdb.d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='!mysql00' --name mariadb-test -d mariadb:10.1
```
root !mysql00


## 2. Verticle apps
```
> docker run -ti --link mariadb-test -p 7070:7070 core/verticle
```

- entrypoint를 직접 지정하여 내부를 확인하는 경우
```
> docker run -ti --entrypoint=/bin/bash core/verticle
```

## 3. test data_api web
```
> curl http://<ip>:7070/api/v1/workflow

> http://localhost:7070/api/v1/common/webhdfs/liststatus
> http://<ip>:7070/api/v1/common/webhdfs/liststatus?webhdfsUrl=http://1<ip>:50070/webhdfs/v1/user/08398/dpExport.sh
```



# PART2. Run docker using docker-comopse






# PART3. RUn docker using rancher web-ui
## 1. Run rancher server
- start rancher server
```
> sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:v1.5.6
```

## 2. Add rancher host
- Infra

## 3. Create stack for Data-api application
- "Menu > STACKS > User" click
- "Add Stack" click
```
Name : data_api
Import compose 에서 compose.yml파일을 선택한다.
 - docker-comopse : rancher/docker-comopse.yml
 - rancher-compose : rancher/rancher-compose.yml
```
- "Create" click


# 4. CReate stack for hdfs using catalog
- http://qiita.com/cyberblack28/items/4c9c36f3a49622594c55 참고
- "Menu > CATALOG > 'Hadoop + Yarn' > View Detail" click
- 필요한 설정을 변경하고 "Launch" click

- Connect to Hadoop
 * http://<Hadoop_namenode-primary_1>:50070
 * Hadoop_namenode-primary_1의 IP는 rancher ui에서 확인이 가능





# PART4. Run docker using Ansible 2

- github link







# PART 9. Troubleshooting

## Rancher haproxy loadbalancer 추가시 오류
### [문제]
- go 언어에서 /etc/resolv.conf 파일을 파싱하면서 발생한 오류
### [해결]
- 해당 문구 option ...을 삭제 후 재구동


timedatectl set-timezone Asia/Seoul
