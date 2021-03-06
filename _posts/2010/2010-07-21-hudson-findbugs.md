---
layout: single
title: hudson - FindBugs 연동하기
date: 2010-07-21 08:40:01.000000000 +09:00
categories:
- development
tags: [ci, hudson, findbugs]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
"내가 작성한 코드는 잘 작성한 것일까?"
내가 작성한 코드가 이상없이 동작하는지 검사하기 위해, JUnit 등을 사용하여 테스트를 수행해 왔다면,
이제 이런 질문을 던져볼 만도 하다. 
프로그래머가 작성한 코드는 "논리"의 집합이다.

그렇다면, 테스트케이스는 "그 논리가 적합한가?" 혹은 "그 논리에 헛점이 있는가?"를 검증하기 위한
것이라고 할 수 있을 것이다.
그러면, 그 "논리를 세우는 방법이 잘 되어 있는가?"를 검증하는 방법도 있을 법하다.
그래서, "코드검사"를 수행한다!! 
코드 검사는 내가 작성하는 코드가 표준에 맞는지, 어떤 잠재적인 오류 패턴을 내포하고 있는지 등을
검사해 준다.

CheckStyle, PMD, FindBugs 등 여러 가지가 있으나, FindBugs를 hudson에 연동하여 사용해 보자.

### FindBugs는 어디에?
FIndBugs는 <a title="[http://findbugs.sourceforge.net]로 이동합니다." target="_blank" href="http://findbugs.sourceforge.net">http://findbugs.sourceforge.net</a> 로부터 정보를 얻을 수 있다.
다운로드는 물론이고, 설명도 잘 되어 있다.

### 설정과정은?
지금까지 hudson과 연결한 다른 플러그인의 설치 방법과 크게 다르지 않다.
* FindBugs 설치하기/ 환경변수 등록하기
* Ant task에 FindBugs 등록하기
* Hudson내 설정하기
의 절차를 따를 것이다.

### FindBugs 설치하기
왼쪽 메뉴에 크게 Downloads 섹션이 있다.
링크를 클릭하고, findbugs-1.3.9.zip을 다운 받았다.
이제 원하는 곳에 설치하자. 
설치라고 해봐야, 압축풀고 환경변수에 등록하는 일 밖에 없다.
환경변수에

```
FINDBUGS_HOME=findbugs 설치디렉토리
```
로 지정하였다.

### Ant Task에 FindBugs 등록하기
FindBugs 홈페이지에 자세한 설명이 되어 있다.

세부 내용은 다음 링크를 참조하자. <a title="[http://findbugs.sourceforge.net/manual/anttask.html]로 이동합니다." target="_blank" href="http://findbugs.sourceforge.net/manual/anttask.html">http://findbugs.sourceforge.net/manual/anttask.html</a>

환경변수 설정은 다음과 같이 한다.

```xml
<!-- FindBugs configuration/directories -->
	<property name="findbugs.home"  	value="${env.FINDBUGS_HOME}"/> 
	<path id="findbugs.classpath">
		<pathelement location="${findbugs.home}/lib/findbugs-ant.jar" />
	</path>
```
findbugs라는 task도 다음과 같이 정의한다.

```xml
<!-- target : FindBugs -->
	<taskdef name="findbugs" 
			classname="edu.umd.cs.findbugs.anttask.FindBugsTask" 
			classpathref="findbugs.classpath"/>
	
	<target name="findbugs">
		<mkdir dir="${report.home}/findbugs" />
		<findbugs home="${findbugs.home}"
                output="xml:withMessages"
                outputFile="${report.home}/findbugs/findbugs.xml" 
				excludeFilter="${report.home}/findbugs/findbugs-filter.xml" 
				timeout="1800000" jvmargs="-Xmx512m">
			<sourcePath path="${src.home}" />
			<class location="${build.home}" />
		</findbugs>
	</target>
```

### Hudson내 설정하기
먼저, FindBugs 플러그인을 설치하자. 
Hudson의 왼쪽 메뉴에 있는 "Hudson 관리하기"를 클릭하고 나타나는 화면으로부터, Manage Plugins를 클릭한다.
Availabe 탭을 클릭하여, 목록을 보면 다음과 같이 FindBugs plugin 항목이 보인다. 

<img src="{{ site.baseurl }}/images/201007/cfile5-uf-194512154c4d4bf17360c4.png" class="aligncenter" width="700" height="32" alt="" />

FindBugs를 체크하고, 우측 하단의 Install 버튼을 클릭한다.
해당 플러그인을 설치하면, 다음 화면이 나타난다.

<img src="{{ site.baseurl }}/images/201007/cfile10-uf-175737184c4d4efa0650f7.png" class="aligncenter" width="692" height="228" alt="" />

이제, hudson을 재시작 해보자.
그러나, 아직 findbugs가 실행되는 것은 아니다. 앞에서 설정한 ant task가 실행되도록 설정하지 않았기 때문이다.
해당 프로젝트로부터 configure 를 클릭하여, ant task를 추가하자.
Build 부분에 Inovke Ant를 추가하여, Targets로 findbugs를 기록한다. 
(앞에서 findbugs task를 이미 build.xml에 추가하였다.)

<img src="{{ site.baseurl }}/images/201007/cfile10-uf-18690d194c4d5114caf4eb.png" class="aligncenter" width="700" height="84" alt="" />

이대로 마치면, 다음 빌드에 findbugs는 실행되지만 hudson에서 직접 결과를 볼 수는 없을 것이다.
findbugs의 report를 publish 하자.

Post-build Actions에 가보면, 어느새 Publish FindBugs analysis results라는 옵션이 생겨있을 것이다.
이를 체크하고, 자신의프로젝트/report/findbugs/findbugs.xml 과 같은 형태로 적어주고 저장한다.

<img src="{{ site.baseurl }}/images/201007/cfile3-uf-173265164c4d51fa3d6bc6.png" class="aligncenter" width="700" height="92" alt="" />
이제 새로 빌드해 보자.
그러면, 왼쪽 부분에 FindBugs Warnings라는 메뉴가 생길 것이다.

<img src="{{ site.baseurl }}/images/201007/cfile23-uf-124c6f184c4d52953f4c41.png" class="aligncenter" width="187" height="305" alt="" />

FindBugs가 무엇을 했는지는 클릭해 보면 알 수 있다.
<img src="{{ site.baseurl }}/images/201007/cfile28-uf-206bfa1e4c4d52ee6445d9.png" class="aligncenter" width="700" height="299" alt="" />

(파일명은 일부러 가렸다.)
문제가 되는 부분의 파일명과 Distribution에 색깔로 위험 정도를 표시해 준다.

세부 항목을 잠시 살펴보면,
<img src="{{ site.baseurl }}/images/201007/cfile10-uf-12781d1d4c4d53b986ec00.png" class="aligncenter" width="700" height="277" alt="" />

해당 부분에서 위와 같은 경고 사항이 발생했고, 해결 방법을 권고해 준다.
참고로 위의 예는, 변수를 선언했으나 사용하지 않았음을 알려주는 경우이다.

FindBugs 등의 코드 검사를 수행시키면, 참 많은 잔소리를 듣게 된다.
그러나, 이는 코드의 품질을 높일 수 있도록 도와주는 즐거운 잔소리가 분명하다.

보다 안전한 코딩을 위해, 기꺼이 잔소리를 들어보자!

