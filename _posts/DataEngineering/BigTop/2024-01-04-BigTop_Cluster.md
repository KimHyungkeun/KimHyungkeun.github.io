---
title: BigTop Cluster
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [DataEngineering, BigTop]
tags:
  [
    DataEngineering,
    BigTop
  ]
---

# BigTop Install - 22.12.04 (최종, Cluster, CentOs7)

- 각 노드들은 인터넷에 이미 연결이 된 상황이며, yum 과 ping 이 가능한 상태
- root 계정으로 진행함
- Node
  - Namenode 1개 / Datanode 2개
  - Oracle VirtualBox
  - OS : CentOS7
  - Memory : 16GB
  - CPU : 2Core
- AWS Cloud를 이용하여 테스트 노드 구성

## 1. 설치 전 설정 (name node, data node 공통)

---

### 1. 네트워크 설정

**Step 1. Hostname : localhost 이외 다른 이름으로 설정. hostname은 FQDN(fully qualified domain name) 으로 설정**.

```bash
[root@node] $ sudo hostnamectl set-hostname <hostname>
```

**Step 2. /etc/hosts 편집. 호스트의 각 클러스터에 IP주소, FQDN설정**

- ifconfig를 통해 ip 확인 후 적용
- Standalone이므로 NAT ip address(enp0s3 확인. 기본값 10.0.2.15) 적용.
- ifconfig 작동 불능 시 yum install net-tools 설치 또는 ip addr로 ip 확인

```bash
[root@node] $ sudo vi /etc/hosts
```

![→ data 노드들에도 똑같은 설정을 해준다.]→ data 노드들에도 똑같은 설정을 해준다.

- 저장한 뒤 reboot 를 통해 시스템을 재시작한 뒤에 변경된 내역 확인

![Untitled 1](https://user-images.githubusercontent.com/12759500/230364402-cdf9fc28-2287-4933-a33a-e72d453c1ca4.png)

### 2. passwordless ssl (namenode만 진행)

**Step 1. SSH key pair 생성**

```jsx
[root@node] $ ssh-keygen
```

**Step 2. Key 저장 위치, passphrase 입력(key 저장 위치 -> 기본값, passphrase -> 없으므로 엔터)**

![Untitled 2](https://user-images.githubusercontent.com/12759500/230364439-5119d13c-180d-4497-901b-f55926636686.png)

**Step 3. 다른 host들에 SSH Public Key 복사. (Stand alone의 경우 생략)**

**※ name node에서만 진행 - data node 는 할 필요가 없다**

```bash
[root@node] $ ssh-copy-id -i /root/.ssh/id_rsa.pub <remote_username>@<server_ip_address>
```

![Untitled 3](https://user-images.githubusercontent.com/12759500/230364484-e2d268a0-2de1-4279-8a87-d6d96bed9ee8.png)

**Step 4. SSH 접속**

- 접속 후 비밀번호 없이 다른노드에 접속이 가능한 것을 확인.

![Untitled 4](https://user-images.githubusercontent.com/12759500/230364551-cc603071-0af5-4e65-ac7a-1d6a538eb08e.png)

### 3. 방화벽 해제 - 만약에 이미 해제되어 있다면 4번으로 넘어갈 것 (공통)

**Step 1. Iptables에 existing rule set 저장**

```bash
[root@node] $ sudo iptables-save > ~/firewall.rules
```

**Step 2. 방화벽 해제**

```bash
[root@node] $ sudo systemctl disable firewalld

[root@node] $ sudo systemctl stop firewalld
```

### 4. SELinux mode 설정 (공통)

**Step 1. SELinux 상태 확인**

```bash
[root@node] $ getenforce
```

**Step 2. output이 enforcing인 경우 계속 진행.**
( output이 Permissive나 Disable인 경우, ‘2.5 NTP Service 활성화’ 부터 진행

**Step 3. /etc/selinux/config 파일 열기**

```bash
[root@node] $ sudo vi /etc/selinux/config
```

**Step 4. SELINUX=enforcing을 SELINUX=permissive로 수정 후 저장, 닫기.**


**Step 5. SELinux 비활성화**

```bash
[root@node] $ sudo setenforce 0
```

[※](https://www.notion.so/1-BigTop-Ambari-9ffdc64c85eb4618b39c3fff590b8685)설치 종료 후 SELinux 재활성화

### 5. NTP Service 활성화 (공통)

Step 1. ntp package 설치

```bash
[root@node] $ sudo yum install ntp -y
```

Step 2. /etc/ntp.conf 파일 수정 (예시)

```bash
[root@node] $ sudo vi /etc/ntp.conf

server 0.pool.ntp.org
server 1.pool.ntp.org
server 2.pool.ntp.org

```


**Step 3. ntpd service 실행**

```bash
[root@node] $ sudo systemctl start ntpd
```

**Step 4. 부팅시 ntpd가 작동되도록 설정**

```bash
[root@node] $ sudo systemctl enable ntpd
```

**Step 5. Ntpd가 정상 작동중인지 확인**

```bash
[root@node] $ sudo systemctl status ntpd
```


**Step 6. NTP 서버와 시스템 시간 동기화**

```bash
[root@node] $ sudo ntpdate -u pool.ntp.org
```

**Step 7. 하드웨어 시간과 시스템 시간 동기화**

```bash
[root@node] $ sudo hwclock --systohc
```

## 2. BigTop Ambari 설치 전 의존성 설치

---

**Step 1. Open JDK 설치 ( name node / data node 공통)**

- open-jdk 1.8 설치
  - 설치가 완료되면 /usr/bin 경로에 java가 생성됨

```bash
[root@node] $ sudo yum install java-1.8.0-openjdk -y
[root@node] $ sudo yum install java-1.8.0-openjdk-devel -y
```

- 환경 변수 등록
  - /usr/bin/java 경로에 심볼릭 링크가 걸려 있으므로 실제 경로를 찾아 환경변수 등록해야 함

```bash
[root@node] $ readlink -f /usr/bin/java
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.352.b08-2.el7_9.x86_64/jre/bin/java
```

- 실제 경로는 찾으면 /etc/profile 을 열고 JAVA_HOME, PATH, CLASSPATH 를 등록할 것

```bash
[root@node] $ vi /etc/profile
...
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.352.b08-2.el7_9.x86_64
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar
...
```

- 등록 완료 후, 아래 명령어 입력

```bash
[root@node] $ source /etc/profile
```

- 등록한 환경 변수가 제대로 적용되었는지 테스트

```bash
[root@node] $ echo $JAVA_HOME
[root@node] $ echo $PATH
[root@node] $ echo $CLASSPATH
```

- java 버전 확인

```bash
[root@node] $ java -version
```

- 참고 링크

[[Linux] CentOS 7에 OpenJDK 1.8 설치](https://bamdule.tistory.com/57)

**Step 2. Maven 및 다른 패키지 설치 ( name node / data node 공통)**

- name node 및 data node 에서도 진행

```bash
[root@node] $ sudo yum install -y rpm-build
[root@node] $ sudo yum install -y python-devel
[root@node] $ sudo yum install -y git
[root@node] $ sudo yum install -y gcc
[root@node] $ sudo yum install -y gcc-c++
[root@node] $ sudo yum install -y wget
[root@node] $ cd ~
[root@node] $ wget https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
[root@node] $ tar -xvzf apache-maven-3.6.3-bin.tar.gz
[root@node] $ mv apache-maven-3.6.3 /usr/local/
```

- maven 설치 끝난 후 /etc/profile 에 MAVEN_HOME 을 설정

```bash
[root@node] $ sudo vi /etc/profile
...
export MAVEN_HOME=/usr/local/apache-maven-3.6.3
export PATH=$PATH:$MAVEN_HOME/bin
...

[root@node] $ source /etc/profile
[root@node] $ mvn -version
```

- mvn -version 으로 설치 여부 및 버전 확인


**Step 3. Database 설치 - mysql ( namenode 01 에서 진행)**

- ambari 의 메타 데이터를 저장하기 위한 mysql 을 설치
- mysql 설치

```bash
[root@node] $ cd ~
[root@node] $ wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
# (-i : 설치, -v : 상세항목 확인, -h : 패키지를 설치할 때 # 표시출력
[root@node] $ sudo rpm -ivh mysql57-community-release-el7-11.noarch.rpm
```

- mysql-community-server 설치

```bash
# (아래 에러 발생시에만 실행하고 다시 mysql-community-server 설치)
# 되도록이면 아래 새로운 GPG-KEY를 발급 받는 것이 좋다
# "The GPG keys listed for the "MySQL 5.7 Community Server"
# repository are already installed but they are not correct for this package.
# Check that the correct key URLs are configured for this repository."
# 라는 에러가 발생하면 새로운 GPG-KEY를 발급받는다
[root@node] $ sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

# mysql-community-server 설치
[root@node] $ sudo yum -y install mysql-community-server
```

- mysql 서비스 시작

```bash

#mysql 서비스 시작
[root@node] $ systemctl start mysqld
#mysql 서비스 enable
[root@node] $ systemctl enable mysqld
# mysql 임시 비밀번호 가져오기
[root@node] $ awk '/temporary password/{print $11}' /var/log/mysqld.log
# mysql 접속확인
[root@node] $ mysql -u root -p
[root@node] $ password: {임시 비밀 번호}
...

# 접속 종료
exit
```

- mysql 설정

```bash
# /etc/my.cnf 파일에 아래 설정 추가.
# mysql 비밀번호 간소화, UTF8 설정.

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8

#비밀번호 관련 보안 설정.
validate_password-policy=LOW
validate_password-length=4
```

- mysql 재시작 후, 비밀번호 변경

```bash
# mysql 재시작
[root@node] $ sudo systemctl restart mysqld

# mysql 접속
[root@node] $ mysql -u root -p
[root@node] $ password : {임시비밀번호}
...
# 비밀번호 변경
ALTER USER "root"@"localhost" IDENTIFIED BY "root";

# 재접속
exit
...

[root@node] $ mysql -u root -p
[root@node] $ Enter password : "root"
```

- database 생성하기

```bash
# database 생성.
create database ambari;
create database hive;

# ambari 계정 생성 (hive 계정생성은 하지 않아도 된다.)
create user 'ambari'@'localhost' identified by 'ambari';
create user 'ambari'@'%' identified by 'ambari';
grant all privileges on *.* to 'ambari'@'localhost' identified by 'ambari' with grant option;
grant all privileges on *.* to 'ambari'@'%' identified by 'ambari' with grant option;
flush privileges;

# hive 계정 생성
create user 'hive'@'localhost' identified by 'hive';
create user 'hive'@'%' identified by 'hive';
grant all privileges on *.* to 'hive'@'localhost' identified by 'hive' with grant option;
grant all privileges on *.* to 'hive'@'%' identified by 'hive' with grant option;
flush privileges;

```

## 3. Apache BigTop 설치 및 실행

---

- git clone 을 할 경우 현재 개발중인 최신 버전을 받는 것이므로 ambari 실행 및 설치 중 에러가 발생할 수 있으므로 이전 버전 브랜치(tag)에서 받을 수도 있다.

```bash
[root@node] $ tar -xvf https://github.com/apache/bigtop/archive/refs/tags/rel/3.1.1.tar.gz
```

- 아래 설치하는 git 버전의 기준은 master branch 를 기준으로 한다.

**Step 1. Apache Bigtop 은 github 에 올라온 버전을 설치**

```bash
[root@node] $ cd /usr/local/lib
[root@node] $ git clone https://github.com/apache/bigtop.git

# 특정 tag를 지정하여 다운로드 하고 싶을 경우, 아래와 같이 진행
# [root@node] $ git clone -b <tag> https://github.com/apache/bigtop.git
```

**Step 2. clone 받은 후 다음 명령어를 순차적으로 입력**

- mvn, java 가 제대로 설치가 안되어 있다면 도중에 설치가 실패할 수도 있다
- name node 실행

```bash
[root@node] $ cd bigtop

[root@node] $ ./gradlew task --all
[root@node] $ ./gradlew task ambari-pkg
[root@node] $ ./gradlew task bigtop-ambari-mpack-pkg
[root@node] $ ./gradlew task bigtop-utils-pkg
```

- data node 실행

```bash
cd bigtop

[root@node] $ ./gradlew task --all
[root@node] $ ./gradlew task ambari-pkg
```

- ambari-pkg 설치는 컴퓨터 사양에 따라 다르지만 기본 2-3시간 정도가 소요됨.
- 설치 도중에 네트워크 상태에 따라 error 가 나며 fail 이 뜰 수 있지만, 다시 해당 명령어를 실행하면 됨.
- ambari-pkg 설치 시 HBase 에서 진도가 넘어가지 않을 수 있는데, 에러가 난 것이 아니라 오래걸리는 것이라 기다린다.
- 위 작업이 모두 완료되었을 시 아래와 같이 생성
  - **name node**
  ![Uploading Untitled 9.png…]()
  - data node
   ![Uploading Untitled 10.png…]()

**Step 3. 아래 경로에 들어가서 yum install 실시**

- **Name Node 실행**

```bash
[root@node] $ yum install -y /usr/local/lib/bigtop/build/ambari/rpm/RPMS/noarch/ambari-agent-2.7.5.0-1.el7.noarch.rpm
[root@node] $ yum install -y /usr/local/lib/bigtop/build/ambari/rpm/RPMS/noarch/ambari-server-2.7.5.0-1.el7.noarch.rpm
[root@node] $ yum install -y /usr/local/lib/bigtop/build/bigtop-utils/rpm/RPMS/noarch/bigtop-utils-3.2.0-1.el7.noarch.rpm
[root@node] $ yum install -y /usr/local/lib/bigtop/build/bigtop-ambari-mpack/rpm/RPMS/noarch/bigtop-ambari-mpack-2.7.5.0-1.el7.noarch.rpm

# yum 이 안될 경우에는 rpm -ivh [파일 이름] 으로 실시
```

- **Data Node 실행**

```bash
[root@node] $ yum install -y /usr/local/lib/bigtop/build/ambari/rpm/RPMS/noarch/ambari-agent-2.7.5.0-1.el7.noarch.rpm

# yum 이 안될 경우에는 rpm -ivh [파일 이름] 으로 실시
```

**Step 4. 상세설정 시작**

```bash
sudo ambari-server setup
```

- 모두 엔터를 치고 넘기며, 권장 사양 대로 설치

**Step 5. 설정이 완료 되었으면 DB 테이블 입력 및 서버 실행**

- **외부 sql 파일 실행으로 실행**

```bash
# mysql 접속
[root@node] $ mysql -u root -p
[root@node] $ password : "root"
...
# ambari meta table 생성
use ambari;
SOURCE /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql;

exit;
...
```

```bash
# mysql jdbc 커넥터 설치
[root@node] $ sudo yum install mysql-connector-java* -y
# mysql 커넥터 경로 설정
[root@node] $ sudo ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar
# mpack 설치: 이것이 있어야 ambari 설치시에 필요한 Hadoop ecosystem stack(BGTP)이 등록 된다
[root@node] $ sudo ambari-server install-mpack --mpack=/usr/lib/bigtop-ambari-mpack/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT-bgtp-ambari-mpack.tar.gz --verbose
# ambari server 시작
[root@node] $ sudo ambari-server start
```

- 성공하면 아래와 같이 나온다.

![Untitled 11](https://user-images.githubusercontent.com/12759500/230364872-d80eba14-c302-4205-a04b-d809f9eebe60.png)

## 4. Ambari UI 기반 설치

---

**Step 1. login**

```bash
id : admin
pw : admin
-> 설치된 하둡 관리시 해당 아이디와 비번을 사용하니 잃어버리지 않도록 주의!
```

![Untitled 12](https://user-images.githubusercontent.com/12759500/230364901-18e5fe31-f65c-47a2-8e40-158a0967eedf.png)

**Step 2. Launch install wizard**

- 로그인이 완료되면 LAUNCH INSTALL WIZARD 버튼을 클릭해 다음 단계로 넘어간다

![Untitled 13](https://user-images.githubusercontent.com/12759500/230364923-fed8c09f-01bf-4acc-9325-dd1c517545d3.png)

**Step 3. Get Started**

- 클러스터 이름을 입력한다

![Untitled 14](https://user-images.githubusercontent.com/12759500/230364952-fd19046d-bd22-493d-a994-9da9e85c66ee.png)

**Step 4. Select Version**

- 설치하고자 하는 패키지들이 있는지 확인하고 next 클릭

![Untitled 15](https://user-images.githubusercontent.com/12759500/230364977-59065cce-4e6e-410f-a31c-59f59d0cec15.png)

- base URL 에서 버전을 최신 버전(아래와 같이)으로 해주어야 한다.

```bash
# 2022.12.04 기준 3.1.1 버전
http://repos.bigtop.apache.org/releases/3.1.1/centos/7/$basearch

# 2023.01.30 기준 3.2.0 버전
http://repos.bigtop.apache.org/releases/3.2.0/centos/7/$basearch
```

⇒ repo 버전이 맞지 않을 경우, 설치하면서 예상하지 못한 에러가 발생할 수 있다.

**Step 5. Install Option**

- Target Hosts 에 연결하려는 hosts name 입력(FQDN 방식으로)

```bash
name01.xxx.io
data01.xxx.io
data02.xxx.io
```

- /root/.ssh/ 내의 id_rsa 파일의 내용을 Host Registration Information 에 붙여 넣는다
- 마지막으로 User 는 root 로 지정하고 Register And Confirm 버튼을 눌러 다음으로 넘어간다

![Untitled 16](https://user-images.githubusercontent.com/12759500/230365093-24856da7-7c4e-4480-823b-51b30536ea52.png)

**Step 6. Confirm Hosts**

- 호스트들에 success 가 뜨면 성공이며 잘 연결된 것이다.

![Untitled 17](https://user-images.githubusercontent.com/12759500/230365120-c74154f8-48a8-4b58-80cb-9da37909e8ff.png)

**Step 7. Choose Services**

- 원하는 에코 시스템을 체크하고 넘어간다
  **(HDFS, YARN, Hive, Zookeeper, Ambari Metrics만 일단 선택 할 것)**

![Untitled 18](https://user-images.githubusercontent.com/12759500/230365147-700bef13-ff6e-412e-90a8-dd941ccd1d18.png)

⇒ 추가 설치 시, 유의점

- HBase 설치 시에 아래와 같이 지정할 것
  - Advanced hbase-site에서 Znode Parent를 “hbase”로 지정
    ![Untitled 19](https://user-images.githubusercontent.com/12759500/230365178-19f10e64-307d-4d23-8a60-aa9db287d202.png)

**Step 8. Assign Master / Assign Slaves and Clients**

- 다음 두 단계에서 각 패키지가 어느 노드에 설치될지 결정한다.

![Untitled 20](https://user-images.githubusercontent.com/12759500/230365211-cc47cab6-c56e-4879-a88a-d9c4f9f158fd.png)
![Untitled 21](https://user-images.githubusercontent.com/12759500/230365727-308f1728-136e-4f7f-8587-e84c8aa56c53.png)

**Step 9. Customize Services**

- DB 와 각 에코시스템의 설정을 설정한다. DB 를 제외하고는 디폴트로 진행

![Untitled 22](https://user-images.githubusercontent.com/12759500/230365728-9db62293-f159-4a34-a47b-8163155a8c39.png)

```bash
username : hive
password : hive
cornfirm password : hive
```

- 다음 넥스트를 누른다
- mysql 은 ambari-server 가 있는 namenode 01 에 설치를 하였으니 주소는 [localhost](http://localhost) 로 지정 후 test connection 을 클릭하여 연켤 체크

```bash
# HIve Database
Existing MySQL / MariaDB
# Database URL
jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true
# HIve Database Type
mysql
# JDBC Driver Class
com.mysql.jdbc.Driver
# database password
root / root
```

![Untitled 23](https://user-images.githubusercontent.com/12759500/230365769-576c6465-644e-4801-a310-69f94c25aabb.png)

- 아래에서는 디렉토리 및 서비스들의 환경설정 할수 있음

![Untitled 24](https://user-images.githubusercontent.com/12759500/230365792-d433eb55-d966-49ff-a05a-4cc456a7f4aa.png)

![Untitled 25](https://user-images.githubusercontent.com/12759500/230365804-893c9f9f-e5fb-4efc-9639-c03ee0f1caf7.png)

- hadoop.proxyuser._ 에는 ‘ _ ‘ 를 입력할 것

![Untitled 26](https://user-images.githubusercontent.com/12759500/230365827-68319a17-1435-4376-b788-f7ed0e3f304f.png)

**Step 10. Review**

- 마지막으로 설정을 확인하고 설치 시작

![Untitled 27](https://user-images.githubusercontent.com/12759500/230365990-6e3cb58b-929f-49d8-9c8a-acd37f186801.png)

**Step 11. Install, Start and Test**


1. 다시 들어가면 아래와 같이 모니터링을 할 수 있는 페이지로 변함 - > `**완료**`

![Untitled 29](https://user-images.githubusercontent.com/12759500/230366088-2ebc9acb-4542-4f16-ac8b-5ecf43640be6.png)


