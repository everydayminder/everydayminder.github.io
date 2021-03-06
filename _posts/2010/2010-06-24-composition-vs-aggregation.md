---
layout: single
title: composition vs. aggregation
date: 2010-06-24 05:41:02.000000000 +09:00
categories:
- development
tags: [UML, aggregation, composition]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
UML 다이어그램에서 특히 집합 관계를 표시할 때, 헷갈리는 용어이다.
'A가 B에 속해있다.' 혹은 'B는 A를 포함한다'의 개념을 표현하고자 할 때 사용하는 표기인데, 
어떤 경우에 이 관계는 composition 또는 aggregation이라고 말할 수 있는 것일까?

이에 대해, googling을 하던 중, 적어도 나에게는 와닿는 포스팅이 있어 
소개하고자 한다. 

물론, 단지 이 글 뿐만 아니라 다른 곳으로부터도 참고하였다.

참고 : 
<a title="[http://www.bestarticle.org/computer/association-aggregation-and-composition-what-are-they-and-how-do-they-differ/]로 이동합니다." target="_blank" href="http://www.bestarticle.org/computer/association-aggregation-and-composition-what-are-they-and-how-do-they-differ/">http://www.bestarticle.org/computer/association-aggregation-and-composition-what-are-they-and-how-do-they-differ/</a>

<span style="font-weight:bold;">Aggregation</span>
<ul style="list-style-type:disc;">
<li>◇--, 실선으로 표시
</li>
<li>대상 객체에 대한 possession을 나타낸다. (ownership 아님)</li>
<li>순환 관계는 안됨 (자기 자신을 포함할 수 없음)</li>
<li>"has-a" 관계</li>
<li>"A가 B를 포함한다"고 했을 경우, B는 A의 구성요소이기도 하지만, A와 별도의 객체이다. 즉, 별도로 존재가능하다.</li>
<li>"근로자"와 "주소" 객체가 있다고 할 경우, 모든 근로자는 주소를 갖고 있지만, 근로자 객체가 소멸한다고 해도 주소는 유효하다.</li>
</ul>

<span style="font-weight:bold;">Composition</span>
<ul style="list-style-type:disc;">
<li>◆--, 실선으로 표시
</li>
<li>대상 객체는 ownership을 나타낸다.</li>
<li>aggregation과 비슷하나, 큰 차이점은 전체-부분에 대한 관계성이며, 부분 객체는 전체 객체 없이 별도로 존재할 수 없다.</li>
<li>즉, 'A가 B를 포함한다"고 했을 경우, composition에서는 A가 없는 상태에서의 B는 존재할 수 없으며, A가 소멸될 경우 B도 함께 소멸한다.</li>
<li>예를 들어, "근로자"와 "email주소" 객체가 있다고 했을 경우, 해당 근로자 객체가 소멸할 경우, 해당 근로자의 email주소 객체도 함께 소멸한다.</li>
<li>"contains-a" 관계</li>
<li>순환 관계는 안됨 (자기 자신을 포함할 수 없음)</li>
</ul>


<span style="font-weight:bold;">Aggregation vs Composition</span>
<ul style="list-style-type:disc;">
<li>aggregation은 "has-a" 관계를, composition은 "contains-a" 또는 "전체-부분" 관계를 표현</li>
<li>aggregation은 "전체"-"부분" 모두 독립적으로 존재할 수 있으나, composition은 "전체"가 "부분"의 생명 주기를 통제한다.</li>
<li>aggregation은 composition에 비해 약한 관계임</li>
<li>composition은 한 개의 전체에 한 개만 속할 수 있으나, aggregation은 여러 개의 전체에 0~n개 속할 수 있다.
</li>
</ul>



