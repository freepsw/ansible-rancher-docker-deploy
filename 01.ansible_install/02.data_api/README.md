# Deploying data api application using rancher-api

## PART 0. load docker image to every rancher hosts

### copy docker image to hosts
```
```

### load a saved image file to docker image
```
> zcat dpcore-data-api-v0.0.1.tar.gz | docker load
```

### 등록된 docker image의 정상동작 확인
```
> docker run -v /home/rts/apps/docker/dump:/docker-entrypoint-initdb.d -p 3306:3306 -e MYSQL_ROOT_PASSWORD='!mysql00' --name mariadb-test -d mariadb:10.1
> docker run -ti -p 7070:7070 --link mariadb-test dpcore/data-api:v0.0.1

#  docker run 시점에 "/etc/hosts"에 host를 추가할 경우 (--add-host hosname:ip)
> docker run -ti --add-host=dataplatform03:169.56.74.87 --add-host=dataplatform03.skcc.com:10.178.50.113 -p 7070:7070 --link mariadb-test dpcore/data-api:v0.0.1
```

- mariadb table alter
```
ALTER TABLE WFS_NODE ADD (owner VARCHAR(255));
ALTER TABLE WFS_NODE_GROUP ADD (owner VARCHAR(255));
ALTER TABLE WFS_WORKFLOW ADD (owner VARCHAR(255));
ALTER TABLE WFS_SCHEDULE ADD (owner VARCHAR(255));
ALTER TABLE WFS_WORKFLOW_JOB ADD (owner VARCHAR(255));
ALTER TABLE WFS_SCHEDULE_JOB ADD (owner VARCHAR(255));
```


# Jobs for deploying data-api

## 1. hosts inventory


## 2. Import images for data-api(vertx apps)



```
> ansible-playbook -i inventories/staging/hosts main.yml
```



- rancher api key (Default)
 * Account API (Default)
   * access-key : 82BEABA420F55EF34EFD
   * secret-key : Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG
 * Environment API Keys : EWF9yRKMEZJHc878V3cKmqbLAJnubjGtTPvm8JHM

- BigData
 * Account API Keys : "publicValue": "DEDBAA19F37F524CAE02", "secretValue": "wkCzvGYk2KkcLhdTW7dCBH5hKoPQdY1wqs6NuSxU",


#### create stack
- curl command line
```
 curl -u "${CATTLE_ACCESS_KEY}:${CATTLE_SECRET_KEY}" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"test-stack-01", "system":false, "dockerCompose":"", "rancherCompose":"", "binding":null}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks'
```

- http request
```
HTTP/1.1 POST /v2-beta/projects/1a5/stacks
Host: 169.56.124.19:8080
Accept: application/json
Content-Type: application/json
Content-Length: 93

{
"name": "test-stack-01",
"system": false,
"dockerCompose": "",
"rancherCompose": "",
"binding": null
}
```
