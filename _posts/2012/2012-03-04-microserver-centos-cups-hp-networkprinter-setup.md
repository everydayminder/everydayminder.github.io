---
layout: single
title: MicroServer + CentOS + CUPS 조합으로 HP MFP 1005를 네트워크 프린터로 쓰기
date: 2012-03-04 15:16:08.000000000 +09:00
categories:
- system
tags: [hp, cups, centos, printer]
author:
  login: everydayminder
  email: 2jhyun@gmail.com
  display_name: everydayminder
  first_name: ''
  last_name: ''
---
지난 번에 CentOS에 hplip를 설치하여, CentOS에서 직접 HP MFP1005로 인쇄가 되는 것을 확인하였다.
내 PC의 문제인지는 모르겠으나, HP에서 제공하는 프린터 드라이버를 다운로드 하여 프린터를 직접 연결하였으나
제대로 인식되지 않아 프린트를 하기 어려웠다.

<p style="margin:0;">
&lt;HP MFP 1005와 궁합이 맞은 우리집 OS들&gt;
* Windows XP : 동작
* Windows Vista : 오류
* Windows 7 : 오류
* CentOS 6 : 동작

그래서, 항상 켜 놓는 MicroServer를 프린터 서버로 활용하게 되었다.

<b>1. CUPS(Common Unix Printing System)의 설치</b>
우선 CUPS가 설치되어 있어야 한다.

<div class="txc-textbox" style="background-color:rgb(254,222,199);border-color:rgb(254,137,67);border-style:dashed;border-width:1px;padding:10px;">
<p style="margin:0;">yum install cups

</div>

<b>2. CUPS 시작</b>
CUPS가 동작하고 있지 않다면, 실행시킨다.
<div class="txc-textbox" style="background-color:rgb(254,222,199);border-color:rgb(254,137,67);border-style:dashed;border-width:1px;padding:10px;">
<p style="margin:0;">service cups start

</div>

<b>3. 로컬에서 CUPS 관리페이지 접속 확인</b>
우선, 서버에서 http://localhost:631로 접속해보자(localhost).
정상적으로 설치되었다면, 다음과 같은 관리 페이지가 나타날 것이다.

<a href="http://luran.zz.mu/wp-content/uploads/1/"></a>
<div>

<b>4. CUPS 환경 설정</b>
이제, 클라이언트 PC에서도 이 관리 사이트에 원격으로 접속할 수 있도록 설정을 변경하자.&nbsp;
/etc/cups/cupsd.conf를 열어보자.

# Restrict access to the server라는 부분을 찾아본다.
외부에서 접속할 시스템의 IP 또는 hostname을 적어주면 되는데, 기본적으로 localhost만 기록되어 있다.
Allow 192.168.1.0/24 를 추가하였다.

# Restrict access to the admin pages를 찾아본다.

admin 페이지를 사용할 수 있는 시스템의 IP 또는 hostname을 적어주면 된다.
마찬가지로, Allow 192.168.1.0/24를 추가하였다.
이 옵션을 적어주지 않으면, 위와 같이 관리자 페이지가 뜨는 상태라 하더라도 Administration 탭을 사용할 수 없다.
설정을 변경하였으므로, service cups restart

<b>5. 방화벽 설정 변경</b>
관리자 페이지는 기본적으로 631 포트를 사용한다.
방화벽 룰에 추가하자.

```
-A INPUT -m state --state NEW -m tcp -p tcp --dport 631 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 631 -j ACCEPT
```
추가하고, service iptables restart


<b>6. 리모트 PC로부터 CUPS 관리페이지 접속 확인</b>
이제 다른 PC에서 관리자 페이지로 접속시도하여, 위와 같은 관리자 페이지가 정상적으로 나타나는지,
Administration 탭에 접근 가능한지 확인해 보자.

위와 같이 성공적으로 관리자 페이지를 설치완료했다면,&nbsp;http://서버IP:631로 접속할 수 있을 것이다. 
나의 경우는 http://192.168.1.x:631로 접속할 수 있고, Administration 탭내의 Manage Printers를 클릭하면,
설치되어 있는 프린터가 정상적으로 나타났다. 프린터 이름을 클릭하면, URL 부분이 다음과 같은 형태의 이름을
나타낼 것이다.

http://서버IP:631/printers/프린터명

<b>7. [Windwos7/Vista] hostfile 변경</b>&nbsp;
실제 접속도 위 형태의 URL을 활용하여 프린터를 설정하면 된다.
그런데, 서버IP를 그대로 하면 프린터 설정이 잘 안된다는 글도 보았고, 나 역시도 직접 IP를 적었더니 인식이 잘 되지
않았다.&nbsp;

대신, hostfile에 서버에서 사용하는 이름 그대로 등록해준다. (이름이 다르면 인식이 안되는 것 같다.)
서버가 192.168.1.x이고, 이름이 aaa 인 경우(uname -a), hostfile에도 그대로 적어준다.
내 hostfile에 bbb라고 적어줬더니 제대로 안되는 것 같다.

참고 :&nbsp;

<a href="http://www.owlfish.com/thoughts/winipp-cups-2003-07-20.html">http://www.owlfish.com/thoughts/winipp-cups-2003-07-20.html</a>&nbsp;

당연히, http://내가적은hostname:631/printers/프린터명 으로 접속시도하면 원래 나타나는 관리자 화면이
보여야 한다.


<b>8. 클라이언트 - 프린터 드라이버 설치</b>
프린터를 이와 같은 방법으로 설정한 이유는, 앞서 언급했듯이 HP 사이트에 올라와 있는 드라이버를
집에 있는 PC들의 OS(Windows7, Vista)들에 &nbsp;각각 설치했으나 정상적으로 프린터를 쓸 수 없었기 때문이다.

프린터 추가를 선택한다.
네트워크 프린터를 선택한다.
이름으로 공유 프린터 선택하라는 란에, 위의 주소를 입력한다. (http://호스트이름:631/printers/프린터명)
모델은 "Generic"으로, 프린터는 "MS Publisher Imagesetter"로 설정한다.
테스트 페이지 인쇄를 해본다. 된다 ^^v

</div>
