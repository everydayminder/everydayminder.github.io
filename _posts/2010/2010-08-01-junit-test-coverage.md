---
layout: single
title: JUnit으로 test coverage를 높이는 습관
date: 2010-08-01 15:33:51.000000000 +09:00
categories:
- development
tags: [test, java, junit, coverage]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
"우리나라 정서상 어렵다, 현실에 맞지 않다"는&nbsp; 말들을 하기도 하고, 듣기도 한다.

Rod Johson이 그의 저서 "Expert one-on-one J2EE Design and Development"에서 
XP 기법을 소개하면서, 그 기법의 모든 것을 따르지는 않더라도 테스트 지향 개발 방법은 바람직하다고 하였다.

테스트에 대한 XP의 기법은,
<ul style="list-style-type:square;">
<li>코드를 작성하기 전에 먼저 테스트 코드를 작성하자
</li>
<li>모든 코드는 단위 테스트 코드를 가져야 하고, 각 단위 테스트는 자동으로 실행될 수 있어야 한다.</li>
<li>버그가 발견되면 버그를 고치기에 앞서, 버그를 다시 재현해 내는 테스트 케이스를 정의한 후에 고쳐야 한다.</li>
</ul>

테스트 코드를 먼저 작성하는 것이 더 유용하다는 관점에 대해서는,
<ul style="list-style-type:square;">
<li>테스트 문서는 스펙 문서에 근거할 뿐만 아니라, 부가적인 정보를 제공한다.</li>
<li>클래스나 컴포넌트 자체로만으로는 불확실한 내용이 테스트에서는 명확해진다. (사용처나 목적을 분명히 알고 작성할 것이므로)</li>
<li>사실, 코드를 모두 작성한 후에 별도로 테스트 코드를 작성하는 것이 훨씬 어렵다.</li>
</ul>

그러므로, 한 메소드씩 작성하되, 코드-테스트코드의 순서가 아니라, 반대인 테스트코드-코드의 순서로 작성해 나가자.

Rod Jonhson은 test 메소드의 이름을 다음과 같이 작성하도록 권고한다.
<div style="border:3px double rgb(121,165,228);background-color:rgb(219,232,251);padding:10px;" class="txc-textbox">test&lt;Method to be tested&gt;&lt;Description of test&gt;
</div>

예를 들면,
```
private void testCommaDelimitedListToStringArrayLegalMatch(String[] components);
public void testCommaDelimitedListToStringArrayMatchWords();
public void testCommaDelimitedListToStringArraySingleString();
```
단순히 테스트의 coverage를 높이기 보다는, 테스트의 메소드 이름만으로도 테스트의 성격을 알 수 있도록 작성해 보자.
테스트내에서도 반복되는 테스트의 경우 private로 작성하고, 이를 활용하자.

위의 예는,
```
commaDelimitedListToStringArray(String s);
```
를 테스트한 예이다.

그리고, 테스트코드도 꾸준히 버전 관리를 해야 한다.
