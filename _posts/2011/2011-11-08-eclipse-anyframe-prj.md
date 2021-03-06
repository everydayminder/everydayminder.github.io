---
layout: single
title: Eclipse + Anyframe IDE를 활용한 Anyframe 프로젝트 만들기
date: 2011-11-08 12:26:56.000000000 +09:00
categories:
- development
tags: [anyframe, eclipse, maven]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
지난 번에 Eclipse에 설치한 Anyframe IDE를 활용하여 Anyframe 프로젝트를 만들어 보려고 한다.
<b>1. Anyframe IDE를 활용한 프로젝트 생성</b>
<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile25-uf-13160e344eb91e8e152a50.png" class="aligncenter" width="527" height="501" alt="" />

File > New > Other > Anyframe > Project를 선택한다.
(Anyframe 항목이 보이지 않는다면, 아마도 이전 단계에서 Anyframe IDE가 제대로 설치되지 않았기 때문일 것이다.)

<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile24-uf-15291e3a4eb91f1d07692b.png" class="aligncenter" width="526" height="647" alt="" />
프로젝트 관련 항목을 입력하자.
<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile2-uf-114a813d4eb91f862b9f85.png" class="aligncenter" width="524" height="645" alt="" />

DBMS를 hsqldb로 해보자.
만약, 앞서 anyframe 사이트에서 안내하는 예제를 실행했었다면, hsqldb jar 파일이 local repository에 이미 존재할 것이다.
그렇지 않다면, hsqldb jar를 별도로 다운로드하여 Driver Jar Path에 별도로 지정해주어야 한다.

<b>2. 프로젝트를 생성했는데 에러가 난다?</b>
내 경우, 프로젝트를 새로 생성했는데, 프로젝트 view에 엑스박스가 가득했다.
<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile25-uf-174961334eb94c0d255b4c.png" class="aligncenter" width="248" height="228" alt="" />

처음에는 심지어 pom.xml에도 에러마크가 떴다.
다음과 같은 내용을 검토하자.

<b>1) m2eclipse 플러그인이 설치안되어 있는 경우</b>
  이전 포스트에서, Anyframe IDE를 설치할 때 관련 플러그인을 찾아서 설치하라고 체크박스에 체크했는데도
  실제로는 설치가 되어 있지 않았다. 결국, 나중에 별도로 설치를 해야만 했다.

<b>2) 관련 플러그인들이 다운로드가 안되었을 경우</b>
  내 경우, pom.xml에 에러 마크가 떠있었을 경우, anyframe core 외 2개의 플러그인을 찾을 수 없다는 에러 메시지가 떴다.
  local repository에 직접 들어가서 보니, jar 파일이 있어야 할 곳에 *.jar.lastUpdated라는 확장자 파일들만 생성되어 있었다.

  maven context 메뉴에서 Disable Dependency Management + Enable Dependency Management를 번갈아서 해보고,
  Update Dependencies도 해보았으나 내 경우는 변화가 없었다.

  즉, pom.xml 파일에 표기가 되어 있어도 repository에 다운로드가 정상적으로 되지 않는 상황이었던 것 같다.
  Eclipse상에서는 여러 번, 반복해도 repository에 변화가 없는 것 같아서 command line에서 maven client를 직접 
  실행시켰더니, 다운로드가 된다! (그러나, 어떤 jar는 정상적으로 다운로드 될때까지 반복해서 실행했다.)

  결국, 관련 jar를 모두 다운로드 받고나서야 에러 표시가 사라졌다. (당연하지)

<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile10-uf-173863384eb951801d2e26.png" class="aligncenter" width="248" height="688" alt="" />

<b>3. 프로젝트 실행</b>
WTP에 연동하여, 다음과 같이 등록하자.
<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile9-uf-1528533f4eba11f306521a.png" class="aligncenter" width="526" height="541" alt="" />

그 다음부터는 동일하다. Servers 탭에서 시작시키고, 웹 브라우저에서 동작 여부를 확인하자.
프로젝트가 정상적으로 시작되었다면,
다음과 같은 웹페이지가 보이고, 링크를 클릭하면 영화리스트 조회 등을 할 수 있는 화면이 나타난다.

<p style="margin:0;"><img src="{{ site.baseurl }}/assets/cfile22-uf-143f073d4eba12c233597b.png" class="aligncenter" width="700" height="291" alt="" />

<b>4. New Project는 New가 아니다?
</b>
그런데, Anyframe 프로젝트를 새로 생성했는데 왜 이런 모습이 생기는 것인가?

New Project를 했는데, Sample Project를 보여준다.
개인적인 생각으로는, 현재 Project 메뉴는 Sample Project로 바꾸고, New Project를 별도로 두면 어떨까 싶다.

그냥 틀을 잡아주는 것으로. 물론, 틀을 잡아주려면 그냥 Maven 프로젝트를 새로 만드는 것과 별로 차이는 없을 것 같기도 하다.
