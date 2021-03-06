---
layout: single
title: hudson - 빌드 자동화 설정하기
date: 2010-07-01 02:09:53.000000000 +09:00
categories:
- development
tags: [ci, hudson, jenkins]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
지금까지는 수동으로 Build Now를 클릭하여, build를 하는 것이었다면, 
이제 Continuous Integration을 위해, 소스 변경본을 감지하여 자동으로 프로젝트를 build 하도록 설정을 해야한다.
우선, SVN 설정 부분에서 다음과 같이 체크박스를 설정한다.

<img src="{{ site.baseurl }}/assets/cfile3-uf-1616e4154c2bf80411ce6b.png" class="aligncenter" width="700" height="51" alt="" />
그리고, 주기적으로 소스에 변화가 있는지 검사하도록 다음과 같이 Trigger 옵션을 설정한다.

<img src="{{ site.baseurl }}/assets/cfile25-uf-1965d0194c2bf8380507f7.png" class="aligncenter" width="700" height="124" alt="" />
SCM을 polling 한다는 뜻은, 소스에 변화가 있는지 보고 변화가 있을 경우 build를 수행한다는 뜻이라고 보면 된다.

위의 그림에서 보는 바와 같이,

```
매 5분마다 검사하기 : */5 * * * *
매일 오전 9시에 검사하기 : 00 09 * * *
```

두 개의 옵션을 부여하였다.
이와 같이 설정하기 전에는,

<img src="{{ site.baseurl }}/assets/cfile9-uf-16607f194c2bf89e0cc636.png" class="aligncenter" width="230" height="286" alt="" />
와 같았으나, polling 옵션을 설정 후 일정 시간이 지나자 자동으로 빌드를 시작하였다.
<img src="{{ site.baseurl }}/assets/cfile23-uf-166f18174c2bf8cd28d350.png" class="aligncenter" width="252" height="356" alt="" />

