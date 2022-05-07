# Spark cluster of Data Engineering
## Agenda
1. Spark(pyspark) + MongoDB + Airflow + Kafka + Flink
2. AWS(EKS)

-- Day 1 (4hrs) ---------------------------------------------------------------------------------------

#### Batch data 를 loading 하여 변형하고, 시각화 하는 small project 로 시작 합니다.
#### 먼저, pc(윈도우 기준) 에 작업 환경을 구성할께요. 시간이 괘 걸려요. 구글링해서 아래 코드를 미리 다운로드 해주세요.

1. Visual Studio Code
2. Anaconda 최신 버전 (Anaconda3-2021.11-Windows-x86_64.exe)
3. JDK 1.8 (jdk-8u311-windows-x64.exe)
4. Winzip (평가판)
5. Spark (spark-3.1.2-bin-hadoop3.2.tgz)
6. winutils ( https://github.com/cdarlint/winutils.git )
7. MongoDB Community Server
8. NoSQLBooster for MongoDB

#### 이건, 향후 서버 작업하는데 불편하지 않도록 평소에 리눅스(Linux)를 많이 사용해 보세요.
1. VMware Workstation 16 Player (Non-commercial use only)
2. Centos7 iso 이미지 

-- Day 2 (4hrs) ---------------------------------------------------------------------------------------

#### 모던 데이터엔지니어링 아키텍쳐에 대해 알아보고, 노트북을 사용하여 첫번째 small project 를 시작합니다.
#### Spark 에서 데이터를 로딩하고 변형하여 MongoDB 에 적재합니다. 이때, jupyter notebook 과 spark-submit 를 사용해 봅니다.
1. 모던 데이터엔지니어링 아키텍쳐
2. 가상python에서 ipykernel 생성하기
3. 첫번째 노트북_PySpark 연동
4. 두번째 노트북_csv 파일을 읽어 변환 후 MongoDB에 적재
5. 세번째 노트북_spark-submit
#### 데이터 드리븐 의사결정을 지원하는 시각화 웹서버 어플리케이션을 만들어 보고, Bokeh 라이브러리에 대해 알아 봅니다.
6. Django 애플리케이션에 Bokeh 시각화 통합

-- Day 3 (4hrs) ---------------------------------------------------------------------------------------

#### Data engineering 를 위한 python 를 배워 봅니다.
1. Basics
2. DataTypes
3. Control
4. Functions

-- Day 4 (4hrs) ---------------------------------------------------------------------------------------

5. File Handling
6. OOP
7. Exception Handling

#### PySpark 를 배워 봅니다.
1. SparkSession

-- Day 5 (4hrs) ---------------------------------------------------------------------------------------
#### Hadoop 를 배워 봅니다.
1. Hadoop with yarn
2. Docker

#### PySpark 와 Hadoop를 연결하여 사용하는 것을 배워 봅니다.

#### 과제1) 관심분야(데이터소스)를 조사하고, 데이터 드리븐 의사결정이라는 목적으로 정보화(=시각화)한 후 웹 서버로 만들어 봅니다.
#### 과제 제출 : 2월 30일까지 택1/개인별
- 비즈니스 목표 및 모델 수립
- 인공지능, 빅데이터 서비스에 필요한 모델 정의
- 비즈니스 요구사항 도출 및 정의
- 요구사항 분석 (기능/비기능)
- 빅데이터/인공지능/웹서비스 아키텍처 설계

-- Day 6 (4hrs) ---------------------------------------------------------------------------------------
1. RDD Fundamentals
2. Create RDD
3. RDD Operations
4. Spark Cluster Execution Architecture
5. RDD Persistence
6. Shared variables

-- Day 7 (4hrs) ---------------------------------------------------------------------------------------
1. Spark SQL
2. DataFrame Fundamentals
3. SparkSession Functionalitites
4. Spark DataTypes
5. DataFrame Rows
6. DataFrame Columns

-- Day 8 (4hrs) ---------------------------------------------------------------------------------------
1. DataFrame ETL(Transformations)
2. DataFrame ETL(Extractions)
3. Performance & Optimization

-- Day 9 (4hrs) ---------------------------------------------------------------------------------------
#### PySpark 를 사용하여 Data Pipeline 작성을 시작해 봅니다. -- PySpark + MongoDB + Bokeh
1. PySpark 와 MongoDB로 ETL
2. PySpark 와 MLlib로 ML
3. Data Visualization
4. Data Pipeline Script 작성

-- Day 10 (8hrs) ---------------------------------------------------------------------------------------
#### Apache Airflow 를 사용하여 Pipeline 작성/관리 해 알아봅니다.
1. Airflow Workflow
2. Architecture
3. Install
4. Airflow CLI
5. Airflow UI

-- Day 11 (8hrs) --------------------------------------------------------------------------------------
#### Kafka 를 구축하고 이를 이용한 Programming 해 봅니다.

-- Day 12 (8hrs) --------------------------------------------------------------------------------------
#### Flink 를 구축하고 이를 이용한 Programming 해 봅니다.
#### 최종 과제를 기획(준비)합니다.

-- Day 13 - 15 (24hrs) --------------------------------------------------------------------------------
#### local 환경을 AWS 로 이행합니다.
#### 필요시, Container 기술에 대해 알아보고 Kubernetes 활용 해 봅니다.


-- Day 16 (8hrs) --------------------------------------------------------------------------------------
#### 최종 과제 결과보고서 및 발표 자료를 작성/검토 합니다.
