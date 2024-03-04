---
title: Spark Argument 종류
date: 2024-03-04 00:00:00 +00:00
categories: [DataEngineering, Spark]
tags:
  [
    DataEngineering,
    Spark
  ]
---

# 1. Spark submit의 기본 argument

- master : 접속할 Cluster Manager를 지정 

- deploy-mode " Driver가 지역적으로 실행될지("client"), Cluster의 작업 머신들 중에서 실행될지("cluster") 결정
  - client 모드에서는 spark-submit이 Driver를 자신이 실행되는 머신에서 실행

  - cluster 모드에서는 Cluster의 작업 노드에서 실행되도록 Driver를 전송

  - 기본은 client 모드

- class : Java나 Scala 프로그램을 실행할 때 "main"이 들어 있는 클래스를 지정 

- name : 사람이 읽기 좋게 애플리케이션의 이름을 지정 (Web UI에 표시)

- jars : 사용자 애플리케이션의 ClassPath에 있어야 할 Jar 파일 목록
  - 사용자의 애플리케이션이 외부 Jar 파일들을 사용한다면 이 옵션으로 지정

- files : 애플리케이션의 작업 내에 필요한 파일들의 목록
  - 각 노드에 배포해 주어야 할 데이터 파일 같은 것들을 위한 옵션

- executor-memory : Executor가 쓸 메모리를 Byte 단위로 지정
  - '512m", "15g" 등 큰 용량을 위한 접미어 사용 가능

- driver-memory
  - Driver가 쓸 메모리를 Byte 단위로 지정
  - "512m", "15g" 등 큰 용량을 위한 접미어 사용 가능

## master에 들어갈 값 종류들
- spark://host:port : Spark의 Stand-alone Cluster의 지정한 포트로 접속
  - 기본적으로 7077 포트를 사용

- mesos://host:port : Mesos Cluster에 지정한 포트로 접속
  - 기본적으로 5050 포트를 사용

- yarn : Yarn Cluster에 접속
  - Hadoop 설정 디렉토리를 HADOOP_CONF_DIR 환경 변수에 설정해줘야 함

- local : Local 모드에서 Single 코어로 실행

- local[N] : Local 모드에서 N개 코어로 실행

- local[*] : Local 모드에서 머신이 갖고 있는 만큼의 코어로 실행

# 2. Spark Argument

- Spark Application 을 작성하게 되면 실행시 Custom한 argument 가 필요한 경우가 있다.

- 즉, command 마지막 부분에 argument 들을 나열하면 순서대로 main함수에 args 배열을 통해 전달 가능하다.


```shell
./bin/spark-submit \
  --class <main-class>
  --master <master-url> \
  --deploy-mode <deploy-mode> \
  --conf <key>=<value> \
  ... # other options
  <application-jar> \
  [application-arguments]
```

- **예제 : test.py라는 파일을 하나 만들어서 spark job으로 submit**
```python

from __future__ import print_function

import sys
from random import random
from operator import add

from pyspark.sql import SparkSession


if __name__ == "__main__":
    """
        Usage: pi [partitions]
    """
    spark = SparkSession\
        .builder\
        .appName("PythonPi")\
        .getOrCreate()

    partitions = 2
    n = 100000 * partitions

    def f(_):
        x = random() * 2 - 1
        y = random() * 2 - 1
        return 1 if x ** 2 + y ** 2 <= 1 else 0

    count = spark.sparkContext.parallelize(range(1, n + 1), partitions).map(f).reduce(add)
    print("Pi is roughly %f" % (4.0 * count / n))
    print("partitions", partitions)
    print("sys.argv[1]: ", sys.argv[1])

    spark.stop()

```
```shell
# "MyString"이라는 문자열을 넣어서 인자로 넘어갔는지 확인
./bin/spark-submit --master local ./examples/src/main/python/test.py "MyString"

# 결과 : 성공
Pi is roughly 3.142420
partitions 2
sys.argv[1]:  TestString
```
![image](https://github.com/KimHyungkeun/KimHyungkeun.github.io/assets/12759500/e700d81d-8a80-4890-85d5-84282f49456e)



- 출처1 : https://spark.apache.org/docs/1.1.0/submitting-applications.html
- 출처2 : https://tomining.tistory.com/83 
- 출처3 : https://blog.naver.com/PostView.naver?blogId=8x8x8x8x8x8&logNo=220749568820
