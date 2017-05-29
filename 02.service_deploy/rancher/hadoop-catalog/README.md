# Hadoop + Yarn (Experimental)
- 이 docker-compoe & rancher-compose 파일은
- hadoop을 초기 설치할 때 사용한 설정이다.
- 위의 hadoop 폴더에 있는 compose 파일은 external volume이 생성되었다는 전제로 실행하는 스크립트이다.
- 따라서 처음 hadoop을 설치할 때, 위의 hadoop 폴더의 compose를 사용하면 "external volume don't exist" 오류 발생


### Info:

 This template will install Apache Hadoop 2.7.1 and Yarn on Rancher network. It is recommended that Hadoop be installed on instances with 8+GB of ram. This image also makes use of 'named' volumes and requires Docker 1.9.x (Ideally, 1.9.1), One Hadoop cluster can be deployed per environment. Additional nodes can be added to the cluster, removing nodes is not currently setup.

### Using

Select Hadoop from the Rancher Catalog. Common HDFS options and Yarn/MapReduce memory options are available to set.

Once the values are set, and the cluster is deployed:

On the hosts running the following services you can access

* HDFS manager on: `namenode-primary:50070`.
* Yarn Resource manager is accessible via `yarn-resourcemanager:8088`

Your default HDFS filesystem URL is hdfs://<namenode>:8020 (Only available on Rancher Network)
