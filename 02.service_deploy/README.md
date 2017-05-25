# Deploy docker service using rancher-api

## PART 0. PREREQUISITE

### 1). API Authentication
- API 사용을 위한 KEY 발급
- https://docs.rancher.com/rancher/v1.6/en/api/v2-beta/

#### Rancher API를 위한 KEY 발급
- rancher api에 접근하기 위해 필요한 인증 KEY (Access Key와 Secret Key로 구성됨)
- Rancher Web-UI > API > Keys 클릭
- "Add Account API Key" 클릭하면
 * NAME을 입력 : 본인이 원하는 Key Name(관리용 Key name이므로, 본인이 원하는 이름을 입력)
 * 입력 후, "Create" 버튼을 클릭하면 API KEY가 아래와 같이 생성된다.
 ```
 Access Key (username) : 3D0171ECDCB2F1075540
 Secret Key (password) : yCHjDUnhQc4NuwXc2VT5hJ3CbW7bKa7U7W7xfAsF
 ```
 - 여기서 Access Key는 Web-UI에서 확인이 가능하지만,
 - Secret Key는 별도로 관리해야 한다. (Web에서 확인 불가)

#### Rancher Environment를 위한 KEY 발급
- Environment를 관리하기 위해 필요한 API를 호출할때 필요한 인증 KEY
- Web-UI에서 발급이 가능하며, 선택한 Environment에만 접근이 가능하다.
##### 용어
- Rancher Web-UI에서 "Environment"라는 용어는, API에서는 "Project"와 동일하다.
- 물리적인 자원 그룹을 의미함.

#### Account KEY
- 새로운 Environment를 생성하거나, 다수의 Environment에 접근이 가능하다. ("/v2-beta/projects"로 접근)

### 2). Scoping


## PART 1. Using curl command line for rancher-api




## PART 2. Using java rest api library for rancher-api





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


#### create stack using curl command
 * curl -u "${API_ACCESS_KEY}:${API_SECRET_KEY}"  위에서 발급받은 key를 사용함.
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"test-stack-01", "system":false, "dockerCompose":"", "rancherCompose":"", "binding":null}' \
'http://10.178.50.103:8080/v2-beta/projects/1a5/stacks'
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





### create mongodb service in test-stack
- http://169.56.124.19:8080/v2-beta/projects/1a5/services > create 클릭
```
# curl command
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"mongodb", "stackId":"1st22", "scale":0, "scalePolicy":1, "launchConfig":{"imageUuid":"docker:mongo"}, "secondaryLaunchConfigs":[], "publicEndpoints":[], "assignServiceIpAddress":false, "lbConfig":""}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/services'


- HTTP REQUEST
HTTP/1.1 POST /v2-beta/projects/1a5/services
Host: 169.56.124.19:8080
Accept: application/json
Content-Type: application/json
Content-Length: 200

{
"name": "mongodb",
"stackId": "1st22",
"scale": 0,
"scalePolicy": 1,
"launchConfig": {
"imageUuid": "docker:mongo"
},
"secondaryLaunchConfigs": [ ],
"publicEndpoints": [ ],
"assignServiceIpAddress": false,
"lbConfig": ""
}

```

### activate mongodb service
```
# curl command
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/services/1s33/?action=activate'

# HTTP request
HTTP/1.1 POST /v2-beta/projects/1a5/services/1s33/?action=activate
Host: 169.56.124.19:8080
Accept: application/json
Content-Type: application/json
Content-Length: 2

{ }
```

### Create stack(data-api) using docker-compose
- post로 yaml파일의 내용을 보내기 위해서는 json으로 변경해야 함
- http://convertjson.com/yaml-to-json.htm (yaml to json)
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"data-api", "system":false, "dockerCompose":"{\r\n \"version\": \"2\", \r\n \"services\": {\r\n \"vertx-web\": {\r\n \"image\": \"dpcore/data-api:v0.0.1\", \r\n \"stdin_open\": true, \r\n \"tty\": true, \r\n \"links\": [\r\n \"mariadb-test:mariadb-test\"\r\n ]\r\n }, \r\n \"lb-api\": {\r\n \"image\": \"rancher/lb-service-haproxy:v0.6.4\", \r\n \"ports\": [\r\n \"7070:7070/tcp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.agent.role\": \"environmentAdmin\", \r\n \"io.rancher.container.create_agent\": \"true\"\r\n }\r\n }, \r\n \"mariadb-test\": {\r\n \"image\": \"mariadb:10.1\", \r\n \"environment\": {\r\n \"MYSQL_ROOT_PASSWORD\": \"my-secret\"\r\n }, \r\n \"stdin_open\": true, \r\n \"volumes\": [\r\n \"/home/rts/apps/docker/dump:/docker-entrypoint-initdb.d\"\r\n ], \r\n \"tty\": true, \r\n \"ports\": [\r\n \"3306:3306/tcp\"\r\n ]\r\n }\r\n }\r\n}", "rancherCompose":"{\r\n \"version\": \"2\", \r\n \"services\": {\r\n \"vertx-web\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true\r\n }, \r\n \"lb-api\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true, \r\n \"lb_config\": {\r\n \"certs\": [], \r\n \"port_rules\": [\r\n {\r\n \"priority\": 1, \r\n \"protocol\": \"http\", \r\n \"service\": \"vertx-web\", \r\n \"source_port\": 7070, \r\n \"target_port\": 7070\r\n }\r\n ]\r\n }, \r\n \"health_check\": {\r\n \"healthy_threshold\": 2, \r\n \"response_timeout\": 2000, \r\n \"port\": 42, \r\n \"unhealthy_threshold\": 3, \r\n \"initializing_timeout\": 60000, \r\n \"interval\": 2000, \r\n \"strategy\": \"recreate\", \r\n \"reinitializing_timeout\": 60000\r\n }\r\n }, \r\n \"mariadb-test\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true\r\n }\r\n }\r\n}", "startOnCreate":true, "binding":null}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks'
```



### Create stacks(ichat4) with docker-compose and rancher-compose

```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"ichat4", "system":false, "dockerCompose":"{\r\n \"version\": \"2\", \r\n \"services\": {\r\n \"letschatapplb\": {\r\n \"image\": \"rancher/lb-service-haproxy:v0.6.2\", \r\n \"ports\": [\r\n \"80:80/tcp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.agent.role\": \"environmentAdmin\", \r\n \"io.rancher.container.create_agent\": \"true\"\r\n }\r\n }, \r\n \"database\": {\r\n \"image\": \"mongo\", \r\n \"stdin_open\": true, \r\n \"tty\": true, \r\n \"labels\": {\r\n \"io.rancher.container.pull_image\": \"always\"\r\n }\r\n }, \r\n \"web\": {\r\n \"image\": \"sdelements/lets-chat\", \r\n \"stdin_open\": true, \r\n \"tty\": true, \r\n \"links\": [\r\n \"database:mongo\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.pull_image\": \"always\"\r\n }\r\n }\r\n }\r\n}", "rancherCompose":"{\r\n \"version\": \"2\", \r\n \"services\": {\r\n \"letschatapplb\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true, \r\n \"lb_config\": {\r\n \"certs\": [], \r\n \"port_rules\": [\r\n {\r\n \"priority\": 1, \r\n \"protocol\": \"http\", \r\n \"service\": \"web\", \r\n \"source_port\": 80, \r\n \"target_port\": 8080\r\n }\r\n ]\r\n }, \r\n \"health_check\": {\r\n \"healthy_threshold\": 2, \r\n \"response_timeout\": 2000, \r\n \"port\": 42, \r\n \"unhealthy_threshold\": 3, \r\n \"interval\": 2000, \r\n \"strategy\": \"recreate\"\r\n }\r\n }, \r\n \"database\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true\r\n }, \r\n \"web\": {\r\n \"scale\": 1, \r\n \"start_on_create\": true\r\n }\r\n }\r\n}", "binding":null}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks'
```

### Activate stack(ichat4) services
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks/1st27/?action=activateservices'
```



### create stack(hadoop-1)
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"name":"hadoop", "system":false, "dockerCompose":"{\r\n \"bootstrap-hdfs\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"su -c \\\"sleep 20 && exec /bootstrap-hdfs.sh\\\" hdfs\", \r\n \"net\": \"container:namenode-primary\", \r\n \"volumes_from\": [\r\n \"namenode-primary-data\"\r\n ]\r\n }, \r\n \"sl-namenode-config\": {\r\n \"image\": \"rancher/hadoop-followers-config:v0.3.5\", \r\n \"net\": \"container:namenode-primary\", \r\n \"environment\": {\r\n \"NODETYPE\": \"hdfs\"\r\n }, \r\n \"volumes_from\": [\r\n \"namenode-primary-data\"\r\n ]\r\n }, \r\n \"namenode-config\": {\r\n \"image\": \"rancher/hadoop-config:v0.3.5\", \r\n \"net\": \"container:namenode-primary\", \r\n \"volumes_from\": [\r\n \"namenode-primary-data\"\r\n ]\r\n }, \r\n \"namenode-primary\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"command\": \"su -c \\\"sleep 15 && /usr/local/hadoop-2.7.1/bin/hdfs namenode\\\" hdfs\", \r\n \"volumes_from\": [\r\n \"namenode-primary-data\"\r\n ], \r\n \"ports\": [\r\n \"50070:50070\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.sidekicks\": \"namenode-config, sl-namenode-config, bootstrap-hdfs, namenode-primary-data\", \r\n \"io.rancher.container.hostname_override\": \"container_name\", \r\n \"io.rancher.scheduler.affinity:container_label_soft\": \"io.rancher.stack_service.name=$${stack_name}/yarn-resourcemanager, io.rancher.stack_service.name=$${stack_name}/jobhistory-server\", \r\n \"io.rancher.scheduler.affinity:container_label_ne\": \"io.rancher.stack_service.name=$${stack_name}/datanode\"\r\n }\r\n }, \r\n \"namenode-primary-data\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"volumes\": [\r\n \"hadoop-namenode-primary-config:/etc/hadoop\", \r\n \"/tmp\"\r\n ], \r\n \"net\": \"none\", \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"/bootstrap-local.sh\"\r\n }, \r\n \"datanode-config\": {\r\n \"image\": \"rancher/hadoop-config:v0.3.5\", \r\n \"net\": \"container:datanode\", \r\n \"volumes_from\": [\r\n \"datanode-data\"\r\n ]\r\n }, \r\n \"datanode-data\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"net\": \"none\", \r\n \"volumes\": [\r\n \"hadoop-datanode-config:/etc/hadoop\", \r\n \"/tmp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"/bootstrap-local.sh\"\r\n }, \r\n \"datanode\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"volumes_from\": [\r\n \"datanode-data\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.sidekicks\": \"datanode-config, datanode-data\", \r\n \"io.rancher.container.hostname_override\": \"container_name\", \r\n \"io.rancher.scheduler.affinity:container_label_ne\": \"io.rancher.stack_service.name=$${stack_name}/$${service_name}\", \r\n \"io.rancher.scheduler.affinity:container_label_soft_ne\": \"io.rancher.stack_service.name=$${stack_name}/namenode-primary, io.rancher.stack_service.name=$${stack_name}/yarn-resourcemanager\"\r\n }, \r\n \"links\": [\r\n \"namenode-primary:namenode\"\r\n ], \r\n \"command\": \"su -c \\\"sleep 45 && exec /usr/local/hadoop-2.7.1/bin/hdfs datanode\\\" hdfs\"\r\n }, \r\n \"yarn-nodemanager-config\": {\r\n \"image\": \"rancher/hadoop-config:v0.3.5\", \r\n \"net\": \"container:yarn-nodemanager\", \r\n \"volumes_from\": [\r\n \"yarn-nodemanager-data\"\r\n ]\r\n }, \r\n \"yarn-nodemanager-data\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"net\": \"none\", \r\n \"volumes\": [\r\n \"hadoop-yarn-nodemanager-config:/etc/hadoop\", \r\n \"/tmp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"/bootstrap-local.sh\"\r\n }, \r\n \"yarn-nodemanager\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"volumes_from\": [\r\n \"yarn-nodemanager-data\"\r\n ], \r\n \"ports\": [\r\n \"8042:8042\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.hostname_override\": \"container_name\", \r\n \"io.rancher.sidekicks\": \"yarn-nodemanager-config, yarn-nodemanager-data\", \r\n \"io.rancher.scheduler.affinity:container_label_ne\": \"io.rancher.stack_service.name=$${stack_name}/$${service_name}\", \r\n \"io.rancher.scheduler.affinity:container_label_soft_ne\": \"io.rancher.stack_service.name=$${stack_name}/namenode-primary, io.rancher.stack_service.name=$${stack_name}/yarn-resourcemanager, io.rancher.stack_service.name=$${stack_name}/jobhistory-server, \", \r\n \"io.rancher.scheduler.affinity:container_label\": \"io.rancher.stack_service.name=$${stack_name}/datanode\"\r\n }, \r\n \"links\": [\r\n \"namenode-primary:namenode\", \r\n \"yarn-resourcemanager:yarn-rm\"\r\n ], \r\n \"command\": \"su -c \\\"sleep 45 && exec /usr/local/hadoop-2.7.1/bin/yarn nodemanager\\\" yarn\"\r\n }, \r\n \"jobhistory-server-config\": {\r\n \"image\": \"rancher/hadoop-config:v0.3.5\", \r\n \"net\": \"container:jobhistory-server\", \r\n \"volumes_from\": [\r\n \"jobhistory-server-data\"\r\n ]\r\n }, \r\n \"jobhistory-server-data\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"net\": \"none\", \r\n \"volumes\": [\r\n \"hadoop-jobhistory-config:/etc/hadoop\", \r\n \"/tmp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"/bootstrap-local.sh\"\r\n }, \r\n \"jobhistory-server\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"volumes_from\": [\r\n \"jobhistory-server-data\"\r\n ], \r\n \"links\": [\r\n \"namenode-primary:namenode\", \r\n \"yarn-resourcemanager:yarn-rm\"\r\n ], \r\n \"ports\": [\r\n \"10020:10020\", \r\n \"19888:19888\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.sidekicks\": \"jobhistory-server-config, jobhistory-server-data\", \r\n \"io.rancher.container.hostname_override\": \"container_name\", \r\n \"io.rancher.scheduler.affinity:container_label_soft\": \"io.rancher.stack_service.name=$${stack_name}/yarn-resourcemanager, io.rancher.stack_service.name=$${stack_name}/namenode-primary\"\r\n }, \r\n \"command\": \"su -c \\\"sleep 45 && /usr/local/hadoop-2.7.1/bin/mapred historyserver\\\" mapred\"\r\n }, \r\n \"yarn-resourcemanager-config\": {\r\n \"image\": \"rancher/hadoop-config:v0.3.5\", \r\n \"net\": \"container:yarn-resourcemanager\", \r\n \"volumes_from\": [\r\n \"yarn-resourcemanager-data\"\r\n ]\r\n }, \r\n \"sl-yarn-resourcemanager-config\": {\r\n \"image\": \"rancher/hadoop-followers-config:v0.3.5\", \r\n \"net\": \"container:yarn-resourcemanager\", \r\n \"environment\": {\r\n \"NODETYPE\": \"yarn\"\r\n }, \r\n \"volumes_from\": [\r\n \"yarn-resourcemanager-data\"\r\n ]\r\n }, \r\n \"yarn-resourcemanager-data\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"net\": \"none\", \r\n \"volumes\": [\r\n \"hadoop-yarn-resourcemanager-config:/etc/hadoop\", \r\n \"/tmp\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.container.start_once\": true\r\n }, \r\n \"command\": \"/bootstrap-local.sh\"\r\n }, \r\n \"yarn-resourcemanager\": {\r\n \"image\": \"rancher/hadoop-base:v0.3.5\", \r\n \"volumes_from\": [\r\n \"yarn-resourcemanager-data\"\r\n ], \r\n \"ports\": [\r\n \"8088:8088\"\r\n ], \r\n \"links\": [\r\n \"namenode-primary:namenode\"\r\n ], \r\n \"labels\": {\r\n \"io.rancher.sidekicks\": \"yarn-resourcemanager-config, sl-yarn-resourcemanager-config, yarn-resourcemanager-data\", \r\n \"io.rancher.container.hostname_override\": \"container_name\", \r\n \"io.rancher.scheduler.affinity:container_label_ne\": \"io.rancher.stack_service.name=$${stack_name}/$${service_name}, io.rancher.stack_service.name=$${stack_name}/datanode, io.rancher.stack_service.name=$${stack_name}/yarn-nodemanager\", \r\n \"io.rancher.scheduler.affinity:container_label\": \"io.rancher.stack_service.name=$${stack_name}/namenode-primary\"\r\n }, \r\n \"command\": \"su -c \\\"sleep 30 && /usr/local/hadoop-2.7.1/bin/yarn resourcemanager\\\" yarn\"\r\n }\r\n}", "rancherCompose":"{\r\n \".catalog\": {\r\n \"name\": \"Hadoop + Yarn\", \r\n \"version\": \"2.7.1-rancher1\", \r\n \"description\": \"Hadoop + Yarn\", \r\n \"minimum_rancher_version\": \"v0.46.0\", \r\n \"questions\": [\r\n {\r\n \"variable\": \"cluster\", \r\n \"label\": \"Cluster Name\", \r\n \"description\": \"Name for the stack volumes\", \r\n \"required\": true, \r\n \"default\": \"hadoop\", \r\n \"type\": \"string\"\r\n }, \r\n {\r\n \"variable\": \"dfs_replication\", \r\n \"description\": \"Default number of HDFS replicas\", \r\n \"label\": \"Default DFS Replica Count\", \r\n \"required\": true, \r\n \"type\": \"int\", \r\n \"default\": \"3\"\r\n }, \r\n {\r\n \"variable\": \"yarn_node_manager_cpu_vcores\", \r\n \"description\": \"yarn.nodemanager.resource.cpu-vcores value\", \r\n \"label\": \"Yarn Nodemanager CPU vcores\", \r\n \"default\": \"8\", \r\n \"type\": \"int\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"yarn_node_manager_resource_memory\", \r\n \"description\": \"yarn.nodemanager.resource.memory-mb value\", \r\n \"label\": \"Yarn Nodemanager Memory Value\", \r\n \"default\": \"8192\", \r\n \"type\": \"int\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"yarn_minimum_allocation\", \r\n \"description\": \"yarn.scheduler.minimum-allocation-mb value\", \r\n \"label\": \"Yarn Minimum Memory allocation\", \r\n \"default\": \"1024\", \r\n \"type\": \"int\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"mapreduce_map_memory\", \r\n \"description\": \"mapreduce.map.memory.mb\", \r\n \"label\": \"Mapreduce Map Memory\", \r\n \"default\": \"1024\", \r\n \"type\": \"int\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"mapreduce_reduce_memory\", \r\n \"description\": \"mapreduce.reduce.memory.mb\", \r\n \"label\": \"Mapreduce Reduce Memory\", \r\n \"default\": \"2048\", \r\n \"type\": \"int\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"mapreduce_map_java_opts\", \r\n \"description\": \"mapreduce.map.java.opts\", \r\n \"label\": \"Mapreduce Map Java Opts\", \r\n \"default\": \"-Xmx768m\", \r\n \"type\": \"string\", \r\n \"required\": true\r\n }, \r\n {\r\n \"variable\": \"mapreduce_reduce_java_opts\", \r\n \"description\": \"mapreduce.reduce.java.opts\", \r\n \"label\": \"Mapreduce Reduce Java Opts\", \r\n \"default\": \"-Xmx1536m\", \r\n \"type\": \"string\", \r\n \"required\": true\r\n }\r\n ]\r\n }, \r\n \"namenode-primary\": {\r\n \"scale\": 1, \r\n \"metadata\": {\r\n \"core-site\": {\r\n \"hadoop.proxyuser.hue.hosts\": \"*\", \r\n \"hadoop.proxyuser.hue.groups\": \"*\"\r\n }, \r\n \"hdfs-site\": {\r\n \"dfs.replication\": \"3\", \r\n \"dfs.webhdfs.enabled\": \"true\"\r\n }\r\n }\r\n }, \r\n \"datanode\": {\r\n \"scale\": 1, \r\n \"metadata\": {\r\n \"core-site\": {\r\n \"hadoop.proxyuser.hue.hosts\": \"*\", \r\n \"hadoop.proxyuser.hue.groups\": \"*\"\r\n }, \r\n \"hdfs-site\": {\r\n \"dfs.replication\": \"3\", \r\n \"dfs.webhdfs.enabled\": \"true\"\r\n }\r\n }\r\n }, \r\n \"yarn-resourcemanager\": {\r\n \"scale\": 1, \r\n \"metadata\": {\r\n \"core-site\": {\r\n \"hadoop.proxyuser.hue.hosts\": \"*\", \r\n \"hadoop.proxyuser.hue.groups\": \"*\"\r\n }, \r\n \"hdfs-site\": {\r\n \"dfs-replication\": \"3\", \r\n \"dfs.webhdfs.enabled\": \"true\"\r\n }, \r\n \"yarn-site\": {\r\n \"yarn.nodemanager.resource.cpu-vcores\": \"8\", \r\n \"yarn.nodemanager.resource.memory-mb\": \"8192\", \r\n \"yarn.scheduler.minimum-allocation-mb\": \"1024\", \r\n \"yarn.nodemanager.aux-services\": \"mapreduce_shuffle\", \r\n \"yarn.log-aggregation-enable\": \"true\", \r\n \"yarn.log-aggregation.retain-seconds\": 10800, \r\n \"yarn.log-aggregation.retain-check-interval-seconds\": 3600\r\n }, \r\n \"mapred-site\": {\r\n \"mapreduce.map.memory.mb\": \"1024\", \r\n \"mapreduce.reduce.memory.mb\": \"2048\", \r\n \"mapreduce.map.java.opts\": \"-Xmx768m\", \r\n \"mapreduce.reduce.java.opts\": \"-Xmx1536m\"\r\n }\r\n }\r\n }, \r\n \"jobhistory-server\": {\r\n \"scale\": 1, \r\n \"metadata\": {\r\n \"core-site\": {\r\n \"hadoop.proxyuser.hue.hosts\": \"*\", \r\n \"hadoop.proxyuser.hue.groups\": \"*\"\r\n }, \r\n \"hdfs-site\": {\r\n \"dfs-replication\": \"3\", \r\n \"dfs.webhdfs.enabled\": \"true\"\r\n }, \r\n \"yarn-site\": {\r\n \"yarn.nodemanager.resource.cpu-vcores\": \"8\", \r\n \"yarn.nodemanager.resource.memory-mb\": \"8192\", \r\n \"yarn.scheduler.minimum-allocation-mb\": \"1024\", \r\n \"yarn.nodemanager.aux-services\": \"mapreduce_shuffle\", \r\n \"yarn.log-aggregation-enable\": \"true\", \r\n \"yarn.log-aggregation.retain-seconds\": 10800, \r\n \"yarn.log-aggregation.retain-check-interval-seconds\": 3600\r\n }, \r\n \"mapred-site\": {\r\n \"mapreduce.map.memory.mb\": \"1024\", \r\n \"mapreduce.reduce.memory.mb\": \"2048\", \r\n \"mapreduce.map.java.opts\": \"-Xmx768m\", \r\n \"mapreduce.reduce.java.opts\": \"-Xmx1536m\"\r\n }\r\n }\r\n }, \r\n \"yarn-nodemanager\": {\r\n \"scale\": 1, \r\n \"metadata\": {\r\n \"core-site\": {\r\n \"hadoop.proxyuser.hue.hosts\": \"*\", \r\n \"hadoop.proxyuser.hue.groups\": \"*\"\r\n }, \r\n \"hdfs-site\": {\r\n \"dfs-replication\": \"3\", \r\n \"dfs.webhdfs.enabled\": \"true\"\r\n }, \r\n \"yarn-site\": {\r\n \"yarn.nodemanager.resource.cpu-vcores\": \"8\", \r\n \"yarn.nodemanager.resource.memory-mb\": \"8192\", \r\n \"yarn.scheduler.minimum-allocation-mb\": \"1024\", \r\n \"yarn.nodemanager.aux-services\": \"mapreduce_shuffle\", \r\n \"yarn.log-aggregation-enable\": \"true\", \r\n \"yarn.log-aggregation.retain-seconds\": 10800, \r\n \"yarn.log-aggregation.retain-check-interval-seconds\": 3600\r\n }, \r\n \"mapred-site\": {\r\n \"mapreduce.map.memory.mb\": \"1024\", \r\n \"mapreduce.reduce.memory.mb\": \"2048\", \r\n \"mapreduce.map.java.opts\": \"-Xmx768m\", \r\n \"mapreduce.reduce.java.opts\": \"-Xmx1536m\"\r\n }\r\n }\r\n }\r\n}", "startOnCreate":true, "binding":null}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks'
```

### activateservices (hadoop1)
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks/1st28/?action=activateservices'
```




### export stasck config
```
curl -u "82BEABA420F55EF34EFD:Gy3aG8byAgeAFhCU3GqMj6zjZn6g4HgT4nqr6LaG" \
-X POST \
-H 'Accept: application/json' \
-H 'Content-Type: application/json' \
-d '{"serviceIds":[]}' \
'http://169.56.124.19:8080/v2-beta/projects/1a5/stacks/1st5/?action=exportconfig'
```






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
