---
layout: single
title: Jira에 MySQL 연동하기
date: 2014-03-29 16:20:36.000000000 +09:00
categories: 
tags: [jira]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
어떤 방법을 선택했건, Jira를 띄울 수 있는 상태가 되었다면,
이제 MySQL을 연결해서 사용할 수 있도록 설정을 진행해보자.

## MySQL 계정 설정

atlassian가이드에는 다음과 같이 안내되어 있다.


```
CREATE DATABASE jiradb CHARACTER SET utf8 COLLATE utf8_bin;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,INDEX on &lt;JIRADB&gt;.* TO '&lt;USERNAME&gt;'@'&lt;JIRA_SERVER_HOSTNAME&gt;' IDENTIFIED BY '&lt;PASSWORD&gt;';
flush privileges;
```


DB 접속에 사용할 계정 정보로 빈 칸을 채워넣고 실행하자.
정상적으로 grant가 되었는지는,


```
show grants;
```

를 수행하여 확인할 수 있다.


## MySQL Connector 설치

첫 시도 시, MySQL Connector를 yum을 사용하여 다운로드하고 진행했으나,
DB 연결에서 실패하였다.


```
yum install mysql-connector-java
```

atlassian 가이드에 안내된 바와 같이, MySQL 공식 사이트로부터
(http://dev.mysql.com/downloads/connector/j) connector를 다운로드 하고 진행하니,
별 에러 없이 connection에 성공하였다.

MySQL 사이트로부터 다운로드한 파일로부터, mysql-connector-java.5.x.x-bin.jar를
찾아 jira를 설치한 곳의 lib 디렉토리에 복사한다.
(내 경우, /opt/atlassian-jira에 jira를 설치했고, /opt/atlassian-jira/lib에
해당 connector jar를 복사하였다.)


## Jira 설정 진행

지난 포스팅에서와 같이 Jira를 설치하고, 실행시켰다면 브라우저 상에서
http://:8080으로 접속 가능할 것이다.


이제 DB 종류를 MySQL로,
접속할 주소와, 계정 정보 등을 본인이 설정한 대로 모두 입력하자.

하단의 접속 테스트를 실행하여 정상적으로 연결이 된다고 뜬다면,
다음 단계로 넘어가자.

## 기타 설정

라이센스, 기타 정보 등을 모두 입력하고 나면,
다소 시간이 걸리지만, 설정이 모두 완료될 것이다. (내 경우는 꽤 오랜 시간이
걸려서 첫 화면이 떴다.)


## Trouble shooting

Jira를 설치한 디렉토리의 /logs/catalina.out을 살펴보면 어떤 에러가 발생했는지
확인할 수 있을 것이다.


내 경우는, 

```
ERROR
The Gadget Dashboard bundled plugin is not available. Please contact an administrator to ensure the Gadget Dashboard plugin is enabled!
Perhaps you need to log in to see the page.
If you think this message is wrong, please consult your administrators about getting the necessary permissions.
```

과 같은 에러가 발생하였고, atlassian 사이트에 나와 있는 안내를 참고하여,
$JIRA_HOME/plugins/.osgi-plugins를 삭제하고, Jira를 재시작 하였다.
그 이후, 다시 실행시켰더니 이번에는 몇몇 플러그인이 로딩되지 않았다고 에러 메시지가
떴다. 이 역시 atlassian 사이트로부터 bin/setenv.sh에 선언되어 있는 환경변수를 조정해 보라는
안내를 발견하여, 다음과 같이 옵션을 변경하였다.


```
JVM_SUPPORT_RECOMMENDED_ARGS=300
```

이후, Jira를 시작시켰더니 앞서 발생한 플러그인 로딩 실패와 관련한 에러는 발생하지 않고,
정상적으로 구동 되었다.

## 정리
  - jira를 어떤 방법이든 택하여 설치
  - MySQL Connector 최신 버전 설치
  - Connector jar를 lib에 복사
  - setenv.sh내 환경변수(JVM_SUPPORT_RECOMMENDED_ARGS=300 편집, 메모리 값 조정)
  - atlassian 가이드 많이 찾아보기

