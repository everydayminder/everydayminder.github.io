---
layout: single
title: hudson - javadoc 생성하기
date: 2010-07-20 04:55:02.000000000 +09:00
categories:
- development
tags: [hudson, javadoc, ant, java]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
사실, 개발하면서 주석을 다는 것은 무척이나 <span style="text-decoration:line-through;">흥미로운 </span>귀찮은 일이다.
게다가 포맷을 지키고, 어떤 파라미터가 넘겨지고, 리턴 값은 어떻고,
어떤 상황에서 어떤 exception이 던져진다는 것까지 써야 한다면 더더욱 그렇다.

보통 프로그램부터 작성한 후, 주석을 달라고 한다면,
주석을 다는 것이 아주 하기 싫은 일이 될 가능성이 크다고 생각한다.
주석을 달면서, 코드 리뷰도 하고, 분석도 하고, 수정도 하는 선순환이 되기 보다는
상당히 형식적인 주석 작업이 될 확률이 더 높아진다.

오히려, 보다 양질의 주석을 달기에 좋은 시기는 해당 부분을 프로그램화 할 때라고 생각한다.
모든 프로젝트를 완료한 후, javadoc을 사용하는 대신에
초기부터 javadoc을 사용해 보자.

자신이 작성하는 코드와 비슷한 시기에 산출물을 생성하는 것이다.
생성된 산출물을 확인하여, 편집하는 수고를 나중으로 미루지 말자.

다행히, hudson에서는 기본적으로 javadoc을 publish하는 기능이 내장되어 있다.

### Hudson의 설정 변경하기
프로젝트의 configure 메뉴를 선택한다.
Post-build Actions에 보면, 별도의 플러그인을 설치하지 않아도 javadoc에 대한 옵션을 설정할 수 있도록
되어 있다.

Publish Javadoc 옵션을 체크하면, javadoc 문서의 위치를 지정할 수 있다.
프로젝트명/docs/javadoc 과 같이 적어준다.
(본인의 경우, 프로젝트명/docs/javadoc에 javadoc 문서가 생성되도록 설정해 두었다.)

<img src="{{ site.baseurl }}/images/201007/cfile25-uf-153b1c184c4529a44b60f5.png" class="aligncenter" width="700" height="72" alt="" />

### ant task 추가하기
javadoc의 디렉토리를 다음과 같이 선언하고,
```xml
<!-- javadoc directory -->
	<property name="javadoc.home" 		value="${basedir}/docs/javadoc" />
</pre>
```

javadoc의 task를 다음과 같이 선언한다.
```xml
<!-- target : javadoc -->
	<target name="javadoc">
		<javadoc sourcepath="${src.home}" windowtitle="J's project" 
			destdir="${javadoc.home}" encoding="UTF-8" charset="UTF-8" 
			docencoding="UTF-8">
		</javadoc>
	</target>
```

이제, javadoc의 task까지 준비하였다.
ant task로서의 javadoc을 호출하기만 하면 될 것이다.

### ant task 실행 설정하기
이제, configure에 Build에 보면, 지난 번에 설정한 all.emma.report task가 보일 것이다.
그 밑에 새로운 ant task를 추가하자.

<img src="{{ site.baseurl }}/images/201007/cfile30-uf-124d28154c452bf261db4c.png" class="aligncenter" width="700" height="69" alt="" />
위의 설정으로부터, 새로운 ant task는 javadoc이라고 이름을 붙였다.
(일부러, all.emma.report에서 compile을 하도록 했으므로, 위의 ant task 작성시 depends를 설정하지 않았다.)

설정을 저장하고 나면, hudson 좌측 메뉴에 javadoc 메뉴가 나타날 것이다.

<img src="{{ site.baseurl }}/images/201007/cfile21-uf-1749bb154c452b779bbe8b.png" class="aligncenter" width="205" height="283" alt="" />

Build Now 를 클릭하거나, 자동으로 빌드가 된 후에 javadoc 생성 여부를 확인해보자.
이로써, hudson에서 javadoc을 생성하고 publsih해 보았다.
이제, 개발하면서 자신의 프로젝트가 빌드되면서 생성되는 산출물의 결과도 함께 확인하며,
문서 작성도 함께 해보자.
