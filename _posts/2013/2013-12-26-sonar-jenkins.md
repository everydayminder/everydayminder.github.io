---
layout: single
title: Sonar + Jenkins 설정
date: 2013-12-26 23:01:08.000000000 +09:00
categories: 
- development
tags: [java, sonar, jenkins]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
현재 Jenkins를 통해 CI(Continuouse Integration)를 하고 있고,
코드의 정적 분석을 FindBugs, PMD, CheckStyle을 써서 리포트를 보고 있긴 하지만,
Sonar를 접하고 설치/ 사용해보고자 한다.

Sonar는 www.sonarqube.org에서 세부 정보를 확인할 수 있다.
Installation Guide 링크를 따라 가보니, Confluence를 사용하여 위키를 꾸며놨다.

"Get Started in Two Minutes"의 설명에 따르면,
설치하고, 실행하는 방법은 무척이나 단순해 보인다.
1. SonarQube 배포판을 압축풀고, 실행시킨다.
2. SonarQube Runner 배포판을 압축풀고, 실행시킨다.
3. 샘플 프로젝트를 압축풀고, sonar-runner를 실행시켜서 분석시킨다.
4. 결과를 화면에서 확인한다. (http://localhost:9000)

Sonar에 대한 소개/분석 자료를 통해 좀더 알아보면,
Sonar가 지원하는 DBMS는

```
Apache Derby (내장)
mySQL
Oracle
PostgreSQL
MS-SQL Server
```
등이다.

설치에 앞서 JDK가 1.5+으로 설치되어 있어야 하며,
Runner(소스 분석을 위해 실행시키는 프로그램)로는
- SonarQube Runner
- Maven
- Ant
를 지원한다고 한다.

따라서, 앞서 언급한 Installation guide는 최소한의 설정으로 가장 빨리
한 번 실행시켜보기 위한 설정이라 할 수 있다.

## 0. 설치/테스트 환경
OS : Windows7 (64)
JDK : JDK1.6
DB : -

## 1. 설치
압축을 풀고 (c:\dev\sonarqube-4.0, 이하 $SONAR_HOME), $SONAR_HOME\bin\windows-x86-64\bin에
있는 SartSonar.bat를 실행시킨다.

```
wrapper | --> Wrapper Started as Console
wrapper | Launching a JVM...
jvm 1 | Wrapper (Version 3.2.3) http://wrapper.tanukisoftware.org
jvm 1 | Copyright 1999-2006 Tanuki Software, Inc. All Rights Reserved.
jvm 1 |
jvm 1 | 2013.12.18 11:02:42 INFO Web server is started
```

와 같은 메시지가 보이면서 시작되었다.
브라우저를 통해, localhost:9000으로 접속해봐도 화면이 휑하다.
Sonar의 기본 관리자 계정 정보는 admin:admin이니 접속해 보자.

## 2. 환경설정 확인
$SONAR_HOME\conf\sonar.properties 파일을 확인해 보자.
별도 DB 설정을 하지 않았기에, Apache Derby 내장 DBMS를 사용하도록 설정된 채이다.

```
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar

#----- Embedded database H2
# Note: it does not accept connections from remote hosts, so the
# SonarQube server and the maven plugin must be executed on the same host.
# Comment the following line to deactivate the default embedded database.
sonar.jdbc.url=jdbc:h2:tcp://localhost:9092/sonar
```
와 같은 문구가 보이고,
그 밑으로는 Sonar가 지원하는 여러 다른 종류의 DBMS의 configuration들이 주석처리 되어 있다.
해당 DBMS를 사용할 경우, 변경하여 사용하면 될 것 같다.

## 3. 프로젝트 분석은 어떻게?
Sonar는 분석 결과를 보여주는 웹 애플리케이션이고, 결국 분석은 다른 프로그램들이 도와줘야 한다.
http://docs.codehaus.org/display/SONAR/Analyzing+Source+Code
에 안내된 바에 따르면,

프로젝트 분석은 아래의 방법중 하나를 택하면 된다.
Analyzing with SonarQube Runner (recommended analyzer)
Analyzing with SonarQube Ant Task
Analyzing with Maven
Analyzing with Gradle
CI Engines

Sonar에 안내된 2분안에 시작하기 튜토리얼에서도 샘플 프로젝트 디렉토리로부터, SonarQube Runner를
실행시키는 것으로 프로젝트 등록/분석을 시도하였다.

결국, Runner 등과 같은 프로그램이 분석 결과를 DBMS에 기록을 하면, Sonar 웹은 그 데이터를 DB로부터 읽어서
보여줄 뿐일 것이다.

프로젝트 등록을 위한 UI도 존재하지 않는 것 같은데, 공식 도움말에서도 이런 문구를 발견할 수 있다.
Adding a project to SonarQube is not done through the web interface, but automatically when the project is analyzed for the first time.
어쨌든, 프로젝트를 등록하기 위해서는 일단 분석을 한 번 시도하면 된다는 이야기이다.

## 4. Jenkins + Sonar 설정
나는 Jenkins를 이미 사용하고 있으므로, Jenkins에 Sonar 설정을 하는 방법에 대해 살펴보고자 한다.
Jenkins에 Maven 관련 설정이 되어있다고 가정한다.

Jenkins 관리 > 플러그인관리 > Sonar 관련 플러그인들을 찾아 설치한다. (Jenkins Sonar Plugin, Sonargraph Plugin)
이후, Jenkins를 재시작하고, Jenkins의 global 설정으로부터 Sonar 환경설정을 하자.

Jenkins 관리 > 시스템설정 > Sonar (고급 메뉴) 관련 항목을 지정한다.
Apache Derby 사용 및 기본 설정으로 그대로 사용한다면, 아마 다음과 같이 입력하게 될 것이다.
```
Server URL : http://localhost:9000
Sonar account login : admin
Sonar acocun password : admin
Database URL : jdbc:h2:tcp://localhost:9092/sonar
Database login : sonar
Database password : sonar
```
Sonar Runner에 대한 설정은 별도로 수행하지 않았다.
프로젝트 설정의 Post-build Actions로부터 Sonar를 선택하고, jdk 설치 정보만 변경하고 저장하였다.  (다른 설정은 디폴트 사용)
이후, 빌드를 수행하면, 해당프로젝트의 대시보드에도 Sonar 아이콘이 생겨있고, 링크도 클릭가능하다.
해당 링크를 클릭하면, 결국 처음에 설정한 바와 같이 http://localhost:9000으로 리다이렉션 될 것이다.
그리고, 이전에 비어있던 화면에 무언가 정보가 채워져 있는 것을 발견할 수 있다.

당연히, Jenkins를 통하지 않고도, 브라우저에서 직접 http://localhost:9000을 접속하면 동일한 화면을 볼 수 있다.  (DB에 분석 데이터가 있으니까)

볼 수 있는 내용들은 다음과 같다.
Lines of code
Documentation
Duplications
Complexity
Issues
Package tangle index
Unit Tests Coverage

해당 항목들을 클릭하면, 상세 내용을 볼 수 있다.
이슈들을 클릭하면, 어떤 부분이 문제가 되고, 어떻게 해결하면 좋을지도 조언해 주기 때문에
코드의 퀄리티를 높이는데 큰 도움이 될 것 같다.

Jenkins에 이미 SCM 관련 설정을 해두어, 소스코드 변동에 따른 폴링/자동 빌드를 설정해 두었기 때문에
소스 변동시마다 Sonar도 분석을 자동으로 업데이트 하게 되었다.

## 5. Maven 단독
Maven으로 소스 분석을 하려면, maven 관련 설정을 변경해줘야 한다.
Sonar에 공식적으로 안내된, maven 설정은

```
<settings>
	<profiles>
	<profile>
	<id>sonar</id>
	<activation>
	<activeByDefault>true</activeByDefault>
	</activation>
	<properties>
	<!-- Example for MySQL-->
	<sonar.jdbc.url>
	jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8
	</sonar.jdbc.url>
	<sonar.jdbc.username>sonar</sonar.jdbc.username>
	<sonar.jdbc.password>sonar</sonar.jdbc.password>

	<!-- Optional URL to server. Default value is http://localhost:9000 -->
	<sonar.host.url>
	http://myserver:9000
	</sonar.host.url>
	</properties>
	</profile>
	</profiles>
</settings>
```

실행은,
```
mvn clean install -DskipTests=true
mvn sonar:sonar
```
와 같이 실행하면 된다.
그런데, 위의 설정만으로는 실제 사용시 번거로울 것 같다.

위의 설정에서 언급한 바와 같이, 분석을 어떻게 수행하느냐의 문제에 대해 생각해 보자.
Jenkins에 설정한 경우,
- Who : Jenkins
- How : Maven 또는 Runner
- When : 소스 변경이 되었을 때
로 생각할 수 있는데 비해,

Maven으로 단독 설정할 경우,
- Who : 사용자
- How : Maven
- When : 사용자가 명령어를 실행할 경우
가 되어 버린다. 결국, 소스를 사용자가 직접 최신 버전으로 업데이트 한 후, maven 명령어를 돌려야 의미가 있을 것이다.

즉, 소스 변경과 함께 자동으로 최신 소스를 검사할 수 있도록 설정하지 않고서는 별도의 maven 실행을 통한 소스 검사만으로는
활용도가 낮아진다.

## 6. 요약
- Jenkins와 같은 CI tool과 연계해서 Sonar 검사를 하도록 하거나
- 열심히 알아서 소스 갱신/ 검사 돌리자.

* SCM Activity라는 플러그인이 있는 것을 확인했으나, 주기적으로 폴링하여 소스를 가져오고 검사하는 용도가 아닌
SCM blame 정보를 출력해주기 위한 용도인 듯하다. (http://docs.codehaus.org/display/SONAR/SCM+Activity+Plugin)

*  소스 정적 검사 및 해결책 제시가 제법 괜찮다. 쓸만 한 것 같다.

