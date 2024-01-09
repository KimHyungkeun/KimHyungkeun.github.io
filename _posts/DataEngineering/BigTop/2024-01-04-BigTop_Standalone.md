---
title: BigTop Standalone
date: YYYY-MM-DD HH:MM:SS +00:00
categories: [DataEngineering, BigTop]
tags:
  [
    DataEngineering,
    BigTop
  ]
---

# 1. **BigTop 이란?**

- Apache BigTop은 아파치 재단의 프로젝트로 빅데이터 오픈 소스들에 대한
  (대표적인 것으로 hadoop, spark, hadoop ecosystem projects 등)
  패키징(rpm, deb 등), 테스트, 가상화를 지원해준다.

- 즉, 빅데이터 오픈소스들의 설치를 사용자들이 편하게 할 수 있도록 도와주는 프로젝트이다.

참고 : [https://bigtop.apache.org/](https://bigtop.apache.org/)

# 2. BigTop Ambari를 이용한 Hadoop 설치

**환경 :**

- **VirtualBox (Standalone 설치)**
  - CentOS7
  - CPU 4 core (그 이하로 잡으면, 설치 진행 시 진행이 매우 무뎌짐)
  - Memory 8GB
  - HDD 100GB
- **Bigtop git clone**
  - ambari-pkg :
    \*ambari-server-2.7.5.0-1.el7.noarch.rpm **\***
  - bigtop-ambari-mpack-pkg :
    **\***bigtop-ambari-mpack-2.7.5.0-1.el7.noarch.rpm\*
  - bigtop-utils-pkg :
    **\***bigtop-utils-3.0.1-1.el7.noarch.rpm\*
- **BGTP-1.0**
  - **public repository :** http://repos.bigtop.apache.org/releases/1.0.0/centos/7/x86_64

**참고 :** [https://snowturtle93.github.io/posts/Apache-Bigtop을-이용한-Ambari-설치-및-Hadoop-클러스터-구축/](https://snowturtle93.github.io/posts/Apache-Bigtop%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-Ambari-%EC%84%A4%EC%B9%98-%EB%B0%8F-Hadoop-%ED%81%B4%EB%9F%AC%EC%8A%A4%ED%84%B0-%EA%B5%AC%EC%B6%95/)

## 0) 사전 구성

- **유의점**
  - ambari 설치 진행 시, centos7 기준으로 하단 명령어로 방화벽 내릴 것
    $ systemctl stop firewalld
    $ systemctl disable firewalld

```bash
# ssh 구성
ssh-keygen
(엔터)
(엔터)

scp root@{hostname} mkdir ~/.ssh
scp ~/.ssh/id_rsa.pub root@{hostname}:~/.ssh/
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
Are you sure you want to continue connecting (yes/no)? Enter yes

# 방화벽 해제(Ambari 8080포트 open시 자유자재로 통신하기 위함)
systemctl stop firewalld
systemctl disable firewalld

# hosts 및 hostname 변경
vi /etc/hosts
192.168.xx.xx {hostname} # 입력

vi /etc/hostname
{hostname} # hostname 변경

# 재부팅
reboot
```

## 1) 사전 필요 환경 구성

```bash
# centos7 설치 후 아래 내용 설정
# rpm build, python devel, git설치, gcc, gcc-c++ 설치, java-openjdk 1.8 설치
sudo yum install rpm-build
sudo yum install python-devel
sudo yum install git
sudo yum install gcc
sudo yum install gcc-c++
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel

# apache maven 설치
cd /usr/local
wget https://archive.apache.org/dist/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
tar -xvf apache-maven-3.6.3-bin.tar.gz
ln -s apache-maven-3.6.3 maven

# 환경변수를 통해 maven 경로 지정
vi ~/.bash_profile

*export MAVEN_HOME=/usr/local/maven
PATH=$PATH:$HOME/bin:$MAVEN_HOME/bin
export PATH*

source ~/.bash_profile

```

## 2) bigtop 다운로드

```bash
cd ~
git clone https://github.com/apache/bigtop.git
cd bigtop
# wget https://archive.apache.org/dist/bigtop/bigtop-3.0.1/bigtop-3.0.1-project.tar.gz
# tar -xvf bigtop-3.0.1-project.tar.gz
#cd bigtop-3.0.1

# 실행 가능한 task 모음
./gradlew task --all
```

![Untitled](https://user-images.githubusercontent.com/12759500/230062964-ba01ee5c-9cfe-456c-b4f0-0aea627e300d.png)

## 3) Apache Bigtop을 이용한 빌드

```bash
# Ambari 빌드

# 빌드가 완료되면 {bigtop_home}/build/ambari/rpm/RPMS/noarch 디렉토리에 rpm 파일이 생성
# 최소 경과 시간 : 3h 11m 28s
# ambari-pkg : ambari-donwload -> ambari-tar -> ambari-rpm 순으로 이어지는 task모음
./gradlew task ambari-pkg

# bigtop-ambari-mpack 빌드: Ambari에서 bigtop Stack으로 Hadoop Ecosystem을 설치 할 수 있도록하는 패키징
# 빌드가 완료되면 {bigtop_home}/build/bigtop-ambari-mpack/rpm/RPMS/noarch 디렉토리에 rpm 파일이 생성
./gradlew task bigtop-ambari-mpack-pkg

# bigtop-utils 빌드 : bigtop-ambari-mpack 설치시 필요한 도구
./gradlew task bigtop-utils-pkg
```

- **./gradlew task ambari-pkg 빌드 성공 화면**

![Untitled 1](https://user-images.githubusercontent.com/12759500/230062999-ac82bff1-f573-4712-912a-3bb32058c1ca.png)

## 4) 빌드된 rpm 설치

- **유의점**
  - ambari-rpm 설치 시, 설치 경로까지 명시해서 sudo yum install ~~ 하기

```bash
# 아래 rpm 설치시, 해당 noarch 디렉터리 경로로 지정해서 할것

# ambari-server 설치
sudo yum install {bigtop_home}/build/ambari/rpm/RPMS/noarch/ambari-server-2.7.5.0-1.el7.noarch.rpm

# ambari-agent 설치
sudo yum install {bigtop_home}/build/ambari/rpm/RPMS/noarch/ambari-agent-2.7.5.0-1.el7.noarch.rpm

# bigtop-utils 설치
sudo yum install {bigtop_home}/build/bigtop-utils/rpm/RPMS/noarch/bigtop-utils-3.2.0-1.el7.noarch.rpm

# bigtop-ambari-mpack 설치 : 이것이 있어야 ambari 설치시에 필요한 Hadoop ecosystem stack이 등록 된다
sudo yum install {bigtop_home}/build/bigtop-ambari-mpack/rpm/RPMS/noarch/bigtop-ambari-mpack-2.7.5.0-1.el7.noarch.rpm
```

## 5) mysql 설정

_참고 : [https://seul96.tistory.com/330](https://seul96.tistory.com/330)_

_GPG-KEY 오류 참고 : [https://stackoverflow.com/questions/71239450/gpg-keys-issue-while-installing-mysql-community-server](https://stackoverflow.com/questions/71239450/gpg-keys-issue-while-installing-mysql-community-server)_

```bash
cd ~
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm

# (-i : 설치, -v : 상세항목 확인, -h : 패키지를 설치할 때 # 표시출력
sudo rpm -ivh mysql57-community-release-el7-11.noarch.rpm

# mysql-community-server 설치
yum -y install mysql-community-server

# (아래 에러 발생시에만 실행하고 다시 mysql-community-server 설치)
# "The GPG keys listed for the "MySQL 5.7 Community Server"
# repository are already installed but they are not correct for this package.
# Check that the correct key URLs are configured for this repository."
# 라는 에러가 발생하면 새로운 GPG-KEY를 발급받는다
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022

#mysql 서비스 시작
systemctl start mysqld
#mysql 서비스 허용
systemctl enable mysqld

# mysql 임시 비밀번호 가져오기
awk '/temporary password/{print $11}' /var/log/mysqld.log

# mysql 접속확인
mysql -u root -p {임시비밀번호}

# 접속 종료
exit
```

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

# mysql 재시작
sudo systemctl restart mysqld
```

```bash
# mysql 접속
mysql -u root -p
Enter password : {임시비밀번호}

# 비밀번호 변경
ALTER USER "root"@"localhost" IDENTIFIED BY "root";

# 재접속
exit
mysql -u root -p
Enter password : "root"
```

## 6) Mysql ambari Metadata 생성

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

# ambari meta table 생성
use ambari;
SOURCE /var/lib/ambari-server/resources/Ambari-DDL-MySQL-CREATE.sql;
```

## 7) Ambari 설치

```bash
# mysql jdbc 커넥터 설치
sudo yum install mysql-connector-java*
# mysql 커넥터 경로 설정
sudo ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar
# mpack 설치: 이것이 있어야 ambari 설치시에 필요한 Hadoop ecosystem stack(BGTP)이 등록 된다
sudo ambari-server install-mpack --mpack=/usr/lib/bigtop-ambari-mpack/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT-bgtp-ambari-mpack.tar.gz --verbose
# ambari server 셋업
sudo ambari-server setup
# ambari server 시작
sudo ambari-server start
```

## 8) ambari 접속 ( _{IP_addr}:8080_ )

```bash
1) Step1 : 클러스터 이름 지정
2) Step2 :  사용할 에코시스템 스택 등록(BGTP-1.0.0)
						호스트 이름 등록,
						master쪽 노드의 id_rsa(private key) 입력,
						public repo 입력 : http://repos.bigtop.apache.org/releases/1.0.0/centos/7/x86_64
그 외, master와 slave에 지정할 서비스를 나열
hadoop 셋팅에서 hadoop.proxyuser.*이 필수 입력란인데, 일단 *로 처리
나머지는 지정된 셋팅 그대로 진행
```

![Untitled 2](https://user-images.githubusercontent.com/12759500/230063099-ce9ffd20-9488-46ff-9cc4-5a2d52e5cadb.png)

![Untitled 3](https://user-images.githubusercontent.com/12759500/230063127-2b7406f7-83e7-4456-94ba-cce9434bb85f.png)

![Untitled 4](https://user-images.githubusercontent.com/12759500/230063156-90e9d095-c47e-4fdd-936c-88eebe3e0c13.png)

![Untitled 5](https://user-images.githubusercontent.com/12759500/230063217-845ad27d-d2da-4ed9-9ee8-fa6e901f13e8.png)

![Untitled 6](https://user-images.githubusercontent.com/12759500/230063260-282d52be-2347-45f1-9f84-cb259343223a.png)

![Untitled 7](https://user-images.githubusercontent.com/12759500/230063282-28e3130c-140b-4653-a7bc-fdee8f53ef5c.png)

![Untitled 8](https://user-images.githubusercontent.com/12759500/230063308-c2979998-8a97-4bcf-ab2a-dcdd275eb982.png)

![Untitled 9](https://user-images.githubusercontent.com/12759500/230063399-66e31fda-dac5-460f-86b6-eaa0d052ee7a.png)

![Untitled 10](https://user-images.githubusercontent.com/12759500/230063425-7ef8e876-0aca-4b5f-a7f2-720e9c48d1f5.png)

![Untitled 11](https://user-images.githubusercontent.com/12759500/230063453-bf35ef28-27cb-44a2-ab5d-c898c383c4de.png)

![Untitled 12](https://user-images.githubusercontent.com/12759500/230063469-fdd7ead4-f695-4cf7-a0f0-6e031b5e904e.png)

## 9) 설치 결과

**(Ambari)**

![Untitled 13](https://user-images.githubusercontent.com/12759500/230063507-98b3ca57-388e-413f-901e-87992e8b9c0e.png)

**(ResourceManager)**

![Untitled 14](https://user-images.githubusercontent.com/12759500/230063530-23d8b652-bc07-4c00-97bc-94ded3871cfd.png)

**(Namenode)**
![Untitled 15](https://user-images.githubusercontent.com/12759500/230063587-00718ec5-668e-492c-bf0e-ee3564a53605.png)022%2006%2030%20(Standalone)%20bd624e1dabf34dc2b83cb40eac929c79/Untitled%2015.png>)

(**BGTP-1.0 스택)**

- _BGTP란 ?
  Hortonworks에서 제공하는 하둡 에코시스템 모음을 “HDP”라고 하듯이,
  BigTop에 의해 설치된 Ambari에서 사용하는 설치 Stack은 “BGTP”라 부른다._

![Untitled 16](https://user-images.githubusercontent.com/12759500/230063662-d0d7865c-6d56-46d8-9b05-d28d646641bf.png)

- BGTP-1.0 스택

![Untitled 17](https://user-images.githubusercontent.com/12759500/230063688-63f3848f-6a36-42e5-89a4-671aa414728b.png)

- HDP 3.1 스택 (110.xx 서버)

![Untitled 18](https://user-images.githubusercontent.com/12759500/230063727-b10ebd63-635e-4f47-89c1-e078704765e7.png)

**\***Bigtop 3.1.0 (latest) 기준으로 BGTP-1.0 밖에 존재하지 않고, 포함된 에코시스템 또한
(HDFS, YARN, Zookeeper 밖에 존재하지 않는다)\*

[https://github.com/apache/bigtop/tree/master/bigtop-packages/src/common/bigtop-ambari-mpack/bgtp-ambari-mpack/src/main/resources/stacks/BGTP/1.0/services](https://github.com/apache/bigtop/tree/master/bigtop-packages/src/common/bigtop-ambari-mpack/bgtp-ambari-mpack/src/main/resources/stacks/BGTP/1.0/services)

**+ 추가로 고려해 볼 점**

참고 : [https://snowturtle93.github.io/posts/bigtop-mpack에-Hive-추가/](https://snowturtle93.github.io/posts/bigtop-mpack%EC%97%90-Hive-%EC%B6%94%EA%B0%80/)

**BGTP-1.0의 Hadoop ecosystem 서비스 추가 가능 여부**

![Untitled 19](https://user-images.githubusercontent.com/12759500/230063774-6b8eb1d5-1a7f-4ed0-a3fb-9ffd740f82ec.png)

# 3. Bigtop BGTP에 Hive 설치

**1) 아래 경로에 HIVE 디렉터리 생성**

```bash
mkdir /var/lib/ambari-server/resources/mpacks/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT/stacks/BGTP/1.0/services/HIVE
```

**2) HIVE 링크파일 생성**

```bash
cd /var/lib/ambari-server/resources/stacks/BGTP/1.0/services/
ln -s /var/lib/ambari-server/resources/mpacks/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT/stacks/BGTP/1.0/services/HIVE HIVE
cp -r /var/lib/ambari-server/resources/common-services/HIVE/0.12.0.2.0/* HIVE/
cd HIVE
```

**3) hive-env.xml에 아래 내용 추가**

```bash
vi configuration/hive-env.xml

# 아래 내용 추가
<property>
	<name>hive_security_authorization</name>
	<value>none</value>
	<description>enable or disable the hive client authorization</description>
	<on-ambari-upgrade add="true"/>
</property>

```

**4) metainfo.xml 수정**

```bash
vi metainfo.xml
# 아래와 같이 작성
# webhcat은 사용 X
```

![Untitled 20](https://user-images.githubusercontent.com/12759500/230063812-a79dce28-15b3-4a74-8e44-880aec46a469.png)

![Untitled 21](https://user-images.githubusercontent.com/12759500/230063849-570caa57-a2a3-4eef-875c-be2d24376337.png)

![Untitled 22](https://user-images.githubusercontent.com/12759500/230063876-81019ee5-9e59-4824-ab77-d81a88279caa.png)

**5) 아래 내용의 hiverserver2-site.xml 추가**
(참고 : [https://github.com/snowturtle93/bgtp-mpack/blob/main/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT/stacks/BGTP/1.0/services/HIVE/configuration/hiveserver2-site.xml](https://github.com/snowturtle93/bgtp-mpack/blob/main/bgtp-ambari-mpack-1.0.0.0-SNAPSHOT/stacks/BGTP/1.0/services/HIVE/configuration/hiveserver2-site.xml))

```bash
# configuration 폴더에 hiveserver2-site.xml 추가

<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
   Licensed to the Apache Software Foundation (ASF) under one or more
   contributor license agreements.  See the NOTICE file distributed with
   this work for additional information regarding copyright ownership.
   The ASF licenses this file to You under the Apache License, Version 2.0
   (the "License"); you may not use this file except in compliance with
   the License.  You may obtain a copy of the License at
       http://www.apache.org/licenses/LICENSE-2.0
   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->
<configuration><property require-input="false">
    <name>hive.metastore.metrics.enabled</name>
    <value>true</value>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="true" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes>
        <type>boolean</type>
    </value-attributes>
    <depends-on/>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.service.metrics.reporter</name>
    <value>JSON_FILE, JMX, HADOOP2</value>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="true" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on/>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.service.metrics.hadoop2.component</name>
    <value>hiveserver2</value>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="true" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on/>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.service.metrics.file.location</name>
    <value>/var/log/hive/hiveserver2-report.json</value>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="true" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on/>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.security.authenticator.manager</name>
    <value>org.apache.hadoop.hive.ql.security.SessionStateUserAuthenticator</value>
    <description>Hive client authenticator manager class name. The user-defined authenticator class should implement interface org.apache.hadoop.hive.ql.security.HiveAuthenticationProvider.  </description>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="false" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on>
        <property>
            <name>hive_security_authorization</name>
            <type>hive-env</type>
        </property>
    </depends-on>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.security.authorization.manager</name>
    <value>org.apache.hadoop.hive.ql.security.authorization.plugin.sqlstd.SQLStdHiveAuthorizerFactory</value>
    <description>the hive client authorization manager class name.
    The user defined authorization class should implement interface org.apache.hadoop.hive.ql.security.authorization.HiveAuthorizationProvider.  </description>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="false" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on>
        <property>
            <name>hive_security_authorization</name>
            <type>hive-env</type>
        </property>
    </depends-on>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.security.authorization.enabled</name>
    <value>false</value>
    <description>enable or disable the Hive client authorization</description>
    <display-name>Enable Authorization</display-name>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="true" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes>
        <type>value-list</type>
        <entries>
            <entry>
                <value>true</value>
                <label>True</label>
            </entry>
            <entry>
                <value>false</value>
                <label>False</label>
            </entry>
        </entries>
        <selection-cardinality>1</selection-cardinality>
    </value-attributes>
    <depends-on>
        <property>
            <name>hive_security_authorization</name>
            <type>hive-env</type>
        </property>
    </depends-on>
    <property_depended_by/>
</property><property require-input="false">
    <name>hive.conf.restricted.list</name>
    <value>hive.security.authenticator.manager,hive.security.authorization.manager,hive.users.in.admin.role</value>
    <description></description>
    <filename>hiveserver2-site.xml</filename>
    <deleted>false</deleted>
    <on-ambari-upgrade add="false" delete="false" update="false"/>
    <property-type></property-type>
    <value-attributes/>
    <depends-on>
        <property>
            <name>hive_security_authorization</name>
            <type>hive-env</type>
        </property>
    </depends-on>
    <property_depended_by/>
</property></configuration>
```

**6) linux_param.py 내용 수정**

```bash
# packages/scripts/linux_param.py에서 하단 내용 수정
vi packages/scripts/params_linux.py

hadoop_home = '/usr/lib/hadoop'
hadoop_lib_home = '/usr/lib/hadoop/lib'

hive_bin = '/usr/lib/hadoop/lib'
hive_schematool_ver_bin = '/usr/lib/hive/bin'
hive_schematool_bin = '/usr/lib/hive/bin'
hive_lib = '/usr/lib/hive/bin'
hive_version_lib = '/usr/lib/hive/bin'
hive_cmd = os.path.join(hive_bin, "hive")
hive_var_lib = '/var/lib/hive'
hive_user_home_dir = "/home/hive"
```

![Untitled 23](https://user-images.githubusercontent.com/12759500/230063920-c00a099a-6152-4a87-a772-492805a266cb.png)

7**) 설치완료**

![Untitled 24](https://user-images.githubusercontent.com/12759500/230063948-90d06c31-2509-4877-bde3-960d4d4c6323.png)

![Untitled 25](https://user-images.githubusercontent.com/12759500/230063973-501143f6-060f-4358-bd17-35475de357c2.png)

# 4. 발생한 이슈

**환경 : BigTop git clone (20220609 기준 latest 버전), Standalone**

## 1) 20220607

- _VirtualBox No space left on device 에러 발생_
  **원인 :** df- h 확인 결과, 하드디스크가 6.2G중 6.1G를 사용중 이어서 저장공간이 부족
  **조치 :** 하드디스크 용량을 100G로 수정 후 재시도
  _디스크 확인 및 조정 방법 참고 : [https://wangin9.tistory.com/entry/vbox-centos7-memory](https://wangin9.tistory.com/entry/vbox-centos7-memory)_

## 2) 20220608

- _Ambari를 이용한 Hadoop ecosystem 설치는 진행했으나 YARN만 On되지 않음_
  **원인 :** (NodeManager가 작동하지 않음)

```
Connection failed to http://master:8042/ws/v1/node/info (Traceback (most recent call last):
  File "/var/lib/ambari-agent/cache/common-services/YARN/2.1.0.2.0/package/alerts/alert_nodemanager_health.py", line 171, in execute
    url_response = urllib2.urlopen(query, timeout=connection_timeout)
  File "/usr/lib64/python2.7/urllib2.py", line 154, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/lib64/python2.7/urllib2.py", line 431, in open
    response = self._open(req, data)
  File "/usr/lib64/python2.7/urllib2.py", line 449, in _open
    '_open', req)
  File "/usr/lib64/python2.7/urllib2.py", line 409, in _call_chain
    result = func(*args)
  File "/usr/lib64/python2.7/urllib2.py", line 1244, in http_open
    return self.do_open(httplib.HTTPConnection, req)
  File "/usr/lib64/python2.7/urllib2.py", line 1214, in do_open
    raise URLError(err)
URLError: <urlopen error [Errno 111] 연결이 거부됨>
```

⇒ **20220608 기준 설치 상태 (192.168.56.102 master) - YARN 미작동**

![Untitled 26](https://user-images.githubusercontent.com/12759500/230064027-3c026bf7-401b-4e9e-a5e6-2e8818da7d84.png)

![Untitled 27](https://user-images.githubusercontent.com/12759500/230064094-7fd3edf3-300b-4b66-a1af-6fc108304277.png)

## 3) 20220609

- _YARN 재설치 시도하고 진행한 결과 새로운 내용의 에러 발견_
  (historyserver 실행, resourcemanager 실행 실패)
  _에러 : classpath building에 있어 올바른 spark 버전에 대해 query를 할 수 없다는 내용_

```bash
Traceback (most recent call last):
  File "/var/lib/ambari-agent/cache/stacks/BGTP/1.0/services/YARN/package/scripts/historyserver.py", line 132, in
    HistoryServer().execute()
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/script/script.py", line 352, in execute
    method(env)
  File "/var/lib/ambari-agent/cache/stacks/BGTP/1.0/services/YARN/package/scripts/historyserver.py", line 112, in start
    service('historyserver', action='start', serviceName='mapreduce')
  File "/usr/lib/ambari-agent/lib/ambari_commons/os_family_impl.py", line 89, in thunk
    return fn(*args, **kwargs)
  File "/var/lib/ambari-agent/cache/stacks/BGTP/1.0/services/YARN/package/scripts/service.py", line 90, in service
    try_sleep=1,
  File "/usr/lib/ambari-agent/lib/resource_management/core/base.py", line 166, in __init__
    self.env.run()
  File "/usr/lib/ambari-agent/lib/resource_management/core/environment.py", line 160, in run
    self.run_action(resource, action)
  File "/usr/lib/ambari-agent/lib/resource_management/core/environment.py", line 124, in run_action
    provider_action()
  File "/usr/lib/ambari-agent/lib/resource_management/core/providers/system.py", line 263, in action_run
    returns=self.resource.returns)
  File "/usr/lib/ambari-agent/lib/resource_management/core/shell.py", line 72, in inner
    result = function(command, **kwargs)
  File "/usr/lib/ambari-agent/lib/resource_management/core/shell.py", line 102, in checked_call
    tries=tries, try_sleep=try_sleep, timeout_kill_strategy=timeout_kill_strategy, returns=returns)
  File "/usr/lib/ambari-agent/lib/resource_management/core/shell.py", line 150, in _call_wrapper
    result = _call(command, **kwargs_copy)
  File "/usr/lib/ambari-agent/lib/resource_management/core/shell.py", line 314, in _call
    raise ExecutionFailed(err_msg, code, out, err)
resource_management.core.exceptions.ExecutionFailed: Execution of 'ambari-sudo.sh  -H -E test -f /var/run/hadoop-mapreduce/mapred/mapred-mapred-historyserver.pid && ambari-sudo.sh  -H -E pgrep -F /var/run/hadoop-mapreduce/mapred/mapred-mapred-historyserver.pid' returned 1.

2022-06-09 17:13:42,888 - Unable to query for the correct spark version to use when building classpaths
Traceback (most recent call last):
  File "/var/lib/ambari-agent/cache/stacks/BGTP/1.0/services/YARN/package/scripts/params_linux.py", line 120, in get_spark_version
    spark_classpath_version = stack_select.get_role_component_current_stack_version()
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 340, in get_role_component_current_stack_version
    stack_select_component = get_package_name()
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 109, in get_package_name
    package = get_packages(PACKAGE_SCOPE_STACK_SELECT, service_name, component_name)
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 185, in get_packages
    raise Fail("The stack packages are not defined on the command. Unable to load packages for the stack-select tool")
Fail: The stack packages are not defined on the command. Unable to load packages for the stack-select tool
2022-06-09 17:13:42,888 - Checking version for HISTORYSERVER via distro-select
2022-06-09 17:13:42,888 - Unable to query for the correct spark version to use when building classpaths
Traceback (most recent call last):
  File "/var/lib/ambari-agent/cache/stacks/BGTP/1.0/services/YARN/package/scripts/params_linux.py", line 120, in get_spark_version
    spark_classpath_version = stack_select.get_role_component_current_stack_version()
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 340, in get_role_component_current_stack_version
    stack_select_component = get_package_name()
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 109, in get_package_name
    package = get_packages(PACKAGE_SCOPE_STACK_SELECT, service_name, component_name)
  File "/usr/lib/ambari-agent/lib/resource_management/libraries/functions/stack_select.py", line 185, in get_packages
    raise Fail("The stack packages are not defined on the command. Unable to load packages for the stack-select tool")
Fail: The stack packages are not defined on the command. Unable to load packages for the stack-select tool
```

- 원인 파악 어려움 :
- 위 에러를 구글링 시도 했으나, 전혀 연관된 이슈가 나오지 않음
- latest버전이라 알려지지 않은 이슈가 존재할 것이라 추정하여, 이전 버전의 설치를 시도
- BGTP-1.0의 public repository release를 1.1.1으로 지정했기에 버전 차이에 의한 오류로 추정

- **조치 : 재설치 시도(** **Bigtop git clone, public repo releases/1.0.0 기준으로 재설치 시도 )**

**Case 1)** 설치 성공

- BigTop git clone
- Ambari Pub repo releases 1.0.0

**Case 2)** 설치 실패 (YARN 설치 중 에러 발생)

- BigTop git clone
- Ambari Pub repo releases 1.1.0

![Untitled 26](https://user-images.githubusercontent.com/12759500/230064245-f2fde837-1ddd-4c9a-ad94-9bd0fcf62662.png)

**Case 3)** 설치 실패 (유효하지 않은 Phoenix 설치 경로로 인한 빌드 실패)

- BigTop 3.0.0

```bash

**Download Phoenix:
      [get] Getting: https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz
      [get] To: /root/bigtop-3.0.0/build/ambari/rpm/BUILD/apache-ambari-2.7.5-src/ambari-metrics/ambari-metrics-timelineservice/target/embedded/phoenix.tar.gz
      [get] Error opening connection java.io.FileNotFoundException: https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz
      [get] Error opening connection java.io.FileNotFoundException: https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz
      [get] Error opening connection java.io.FileNotFoundException: https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz
      [get] Can't get https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz to /root/bigtop-3.0.0/build/ambari/rpm/BUILD/apache-ambar   i-2.7.5-src/ambari-metrics/ambari-metrics-timelineservice/target/embedded/phoenix.tar.gz
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary:
[INFO]
[INFO] Ambari Main 2.7.5.0.0 .............................. SUCCESS [  1.002 s]
[INFO] Apache Ambari Project POM 2.7.5.0.0 ................ SUCCESS [  0.009 s]
[INFO] Ambari Web 2.7.5.0.0 ............................... SUCCESS [ 54.534 s]
[INFO] Ambari Views 2.7.5.0.0 ............................. SUCCESS [  1.802 s]
[INFO] Ambari Admin View 2.7.5.0.0 ........................ SUCCESS [01:06 min]
[INFO] ambari-utility 1.0.0.0-SNAPSHOT .................... SUCCESS [  2.480 s]
[INFO] ambari-metrics 2.7.5.0.0 ........................... SUCCESS [  0.149 s]
[INFO] Ambari Metrics Common 2.7.5.0.0 .................... SUCCESS [  6.930 s]
[INFO] Ambari Metrics Hadoop Sink 2.7.5.0.0 ............... SUCCESS [  2.853 s]
[INFO] Ambari Metrics Flume Sink 2.7.5.0.0 ................ SUCCESS [  1.420 s]
[INFO] Ambari Metrics Kafka Sink 2.7.5.0.0 ................ SUCCESS [  1.583 s]
[INFO] Ambari Metrics Storm Sink 2.7.5.0.0 ................ SUCCESS [  1.983 s]
[INFO] Ambari Metrics Storm Sink (Legacy) 2.7.5.0.0 ....... SUCCESS [  2.525 s]
[INFO] Ambari Metrics Collector 2.7.5.0.0 ................. FAILURE [08:38 min]
[INFO] Ambari Metrics Monitor 2.7.5.0.0 ................... SKIPPED
[INFO] Ambari Metrics Grafana 2.7.5.0.0 ................... SKIPPED
[INFO] Ambari Metrics Host Aggregator 2.7.5.0.0 ........... SKIPPED
[INFO] Ambari Metrics Assembly 2.7.5.0.0 .................. SKIPPED
[INFO] Ambari Service Advisor 1.0.0.0-SNAPSHOT ............ SKIPPED
[INFO] Ambari Server 2.7.5.0.0 ............................ SKIPPED
[INFO] Ambari Functional Tests 2.7.5.0.0 .................. SKIPPED
[INFO] Ambari Agent 2.7.5.0.0 ............................. SKIPPED
[INFO] ambari-logsearch 2.7.5.0.0 ......................... SKIPPED
[INFO] Ambari Logsearch Appender 2.7.5.0.0 ................ SKIPPED
[INFO] Ambari Logsearch Config Api 2.7.5.0.0 .............. SKIPPED
[INFO] Ambari Logsearch Config JSON 2.7.5.0.0 ............. SKIPPED
[INFO] Ambari Logsearch Config Solr 2.7.5.0.0 ............. SKIPPED
[INFO] Ambari Logsearch Config Zookeeper 2.7.5.0.0 ........ SKIPPED
[INFO] Ambari Logsearch Config Local 2.7.5.0.0 ............ SKIPPED
[INFO] Ambari Logsearch Log Feeder Plugin Api 2.7.5.0.0 ... SKIPPED
[INFO] Ambari Logsearch Log Feeder Container Registry 2.7.5.0.0 SKIPPED
[INFO] Ambari Logsearch Log Feeder 2.7.5.0.0 .............. SKIPPED
[INFO] Ambari Logsearch Web 2.7.5.0.0 ..................... SKIPPED
[INFO] Ambari Logsearch Server 2.7.5.0.0 .................. SKIPPED
[INFO] Ambari Logsearch Assembly 2.7.5.0.0 ................ SKIPPED
[INFO] Ambari Logsearch Integration Test 2.7.5.0.0 ........ SKIPPED
[INFO] ambari-infra 2.7.5.0.0 ............................. SKIPPED
[INFO] Ambari Infra Solr Client 2.7.5.0.0 ................. SKIPPED
[INFO] Ambari Infra Solr Plugin 2.7.5.0.0 ................. SKIPPED
[INFO] Ambari Infra Manager 2.7.5.0.0 ..................... SKIPPED
[INFO] Ambari Infra Assembly 2.7.5.0.0 .................... SKIPPED
[INFO] Ambari Infra Manager Integration Tests 2.7.5.0.0 ... SKIPPED
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  11:02 min
[INFO] Finished at: 2022-06-14T13:02:08+09:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (phoenix_download) on project ambari-metrics-timelineservice: An Ant BuildException has occured: Can't get ht   tps://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz to /root/bigtop-3.0.0/build/ambari/rpm/BUILD/apache-ambari-2.7.5-src/ambari-metri   cs/ambari-metrics-timelineservice/target/embedded/phoenix.tar.gz
[ERROR] around Ant part ...<get usetimestamp="true" src="https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz" dest="/root/bigtop-3.   0.0/build/ambari/rpm/BUILD/apache-ambari-2.7.5-src/ambari-metrics/ambari-metrics-timelineservice/target/embedded/phoenix.tar.gz"/>... @ 5:299 in /root/bigtop-3.0.0/build/ambari/rpm/BUILD/apache   -ambari-2.7.5-src/ambari-metrics/ambari-metrics-timelineservice/target/antrun/build-Download Phoenix.xml
[ERROR] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
[ERROR]
[ERROR] After correcting the problems, you can resume the build with the command
[ERROR]   mvn <args> -rf :ambari-metrics-timelineservice

⇒ 확인 결과 : 하단 경로로는 tar파일이 존재하지 않음
[https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz](https://downloads.apache.org/phoenix/apache-phoenix-5.0.0-HBase-2.0/bin/apache-phoenix-5.0.0-HBase-2.0-bin.tar.gz)**
```

Case 4) 설치 진행 중 (/sbin/ambari-server ${BuildNumber} 미지정 되었다는 이슈)

- bigtop-3.1.0

![Untitled 28](https://user-images.githubusercontent.com/12759500/230064298-2cb1f88a-99bf-43ae-9498-cfaf70a42cfe.png)

![Untitled 29](https://user-images.githubusercontent.com/12759500/230064311-ab500ae4-720c-402c-b355-64ecb08cb20a.png)

⇒ 다른 설치된 Ambari쪽의 HASH값을 넣어보면, 설치 실패
