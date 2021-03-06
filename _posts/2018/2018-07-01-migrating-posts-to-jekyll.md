---
layout: single
title: Wordpress post를 Jekyll로 이관
categories: life
tags: [wordpress, jekyll]
---

# 개요

기존에 wordpress에 올라가 있던 글들을 Jekyll로 옮기던 과정에서의 lessons를 정리해 둔다. 또 할지도 모르니까.

많은 사람들이, 한결같이 "쉽게 마이그레이션 했다"고 했다.

그러나, 나는 오래 걸렸다. (심지어, 아직도 진행 중이다.) 그래서 적어둬야 한다.



# Exporting from Wordpress

기존의 댓글은 과감히 포기하기로 한다. 

3rd party 플러그인과, 워드프레스 빌트인 플러그인으로 각각 export/ import를 비교해봤으나, 워드프레스 자체 플러그인이 더 깔끔해 보여서, 후자를 택하기로 한다.

워드프레스의 도구 > 글을 선택하여, 글만 전체 export 한다. 한 개의 xml 파일이 생성될 것이다.



# Importing to Jekyll

블로그의 작업 디렉토리로 이동하여, 앞서 export 및 다운로드한 xml 파일을 사용하여 포스트를 import 한다.

```
ruby -rubygems -e 'require "jekyll-import"; JekyllImport::Importers::WordpressDotCom.run({ "source" => "블로그이름.wordpress.2018-06-09.xml" })’ 
```

이제, 로컬에서 jekyll을 띄워서 글들이 제대로 이사왔는지 보자.

```
jekyll serve --port 8080
```

레이아웃이 깨진다.

## 이슈 #1 : 레이아웃 미적용  

콘솔 창에서 다음과 같은 에러 메시지가 뜨는 것을 확인하였다.

```
Build Warning: Layout 'post' requested in _posts/2014-07-03-%eb%a7%a5%ec%97%90%ec%84%9c-%eb%a7%88%ec%9a%b0%ec%8a%a4-%ec%8a%a4%ed%81%ac%eb%a1%a4-%eb%b0%a9%ed%96%a5-%eb%b0%98%eb%8c%80%eb%a1%9c-%ed%95%98%eb%a0%a4%eb%a9%b4.html does not exist.
```

기본 옵션으로 이전해 온 블로그 포스트들이, 기본 레이아웃으로 'post'로 export되어 있어, 위와 같은 warning 메시지가 발생하였다. minimal mistakes 테마를 선택하였기에, 기본 레이아웃을 single로 변경하였다.

```
sed -i "" 's/layout: post/layout: single/g' *.html
```



## 이슈 #2 : Syntax Highlighter 미동작

이렇게 변경하고 보니, warning 메시지는 사라졌는데 기존 글들의 syntax highlighter가 제대로 동작하지 않았다.

 highlight 구문으로 묶으면 syntax highlighter가 동작하나, ```로 묶으면 변환되지 않고 그대로 노출되는 현상을 발견하였다.

확장자가 html이 아니라, md여야 제대로 변환해 주는 것을 확인하였다. 기존에 import한 상태는 모두 확장자가 html로 되어 있던 상태였다. 이에, 다음과 같이 _post/*.html의 확장자를 md로 변경해 주었다.

```
rename 's/.html/.md/' *.html
```

이렇게 변경하고 나니, 레이아웃도 잡히고, syntax highlighter도 일단 동작하는 것을 확인하였다.
