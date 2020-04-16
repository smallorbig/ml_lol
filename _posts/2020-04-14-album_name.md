---
layout: post
title: "Web Scaping - 가수 앨범 리스트 가져오기"
date: 2020-04-14
use_math: true
author: Oh
---

# Intro

자연어 처리를 위해서는 텍스트 데이터, 즉 말뭉치가 필요하다.

말뭉치를 모으기 위해서 필요한 많은 방법이 있겠으나, 잘 알려진 파이썬 크롤링을 간단하게 살펴보자.

`BeautifulSoup`과 `Selenium`을 통해서 원하는 가수의 앨범명을 텍스트로 가져오는 법을 소개한다.

Python 3.7 환경에서 최신 라이브러리를 사용했다.

아래 명령어로 두 라이브러리를 설치하자.

```console
pip install bs4, selenium
```



# BeautifulSoup

검색 포털사이트 다음을 사용해서 **아이유**의 앨범명을 모두 모아보자.

먼저 필요한 라이브러리를 부른다.

```python
import requests
import time
from bs4 import BeautifulSoup as bsoup
from selenium import webdriver
```



검색하고픈 단어를 `query`에 저장하여 다음 검색을 위한 `url`을 생성한다.

`requests`의 `get` 명령어를 사용해서 생성한 `url`의 웹 페이지 내용을 저장한다.

```python
query = "아이유"
url = "https://search.daum.net/search?w=tot&DA=YZR&t__nil_searchbox=btn&sug=&sugo=&q=" + query
req = requests.get(url)
```



`req` 변수에는 주어진 `url`의 내용은 unicode로 적힌 html 코드가 저장되어 있다.

이를 `BeutifulSoup`의 `html.parser`를 사용해 온전한 html 코드로 변환한다.

```python
cont = req.content
soup = bsoup(cont, 'html.parser')
```



이제 `soup` 변수를 살펴보면 아래와 같은 정보를 얻는다.

```python
<!DOCTYPE html>

<html class="unix unknown" lang="ko" xmlns="http://www.w3.org/1999/xhtml">
<head profile="http://a9.com/-/spec/opensearch/1.1/">
<meta content="text/html;charset=utf-8" http-equiv="content-Type"/>
<meta content="IE=edge" http-equiv="X-UA-Compatible"/>
<meta content="off" name="autocomplete"/>
<meta content="always" name="referrer"/>
<meta content="telephone=no" name="format-detection">
<meta content="아이유 – Daum 검색" property="og:title"/>
<meta content="https://search.daum.net/search?w=tot&amp;amp;q=%EC%95%84%EC%9D%B4%EC%9C%A0" property="og:url"/>
<meta content="Daum 검색에서 아이유에 대한 최신정보를 찾아보세요." property="og:description"/>
<meta content="https://search1.daumcdn.net/search/statics/common/img/og_search.png" property="og:image"/>
<meta content="다음검색" property="og:site_name"/>
<title>아이유 – Daum 검색</title>
<link href="//search.daum.net/OpenSearch.xml" rel="search" title="Daum" type="application/opensearchdescription+xml"/>
<link charset="utf-8" href="//search1.daumcdn.net/search/statics/common/css/2020/p_common.0225092140.css" rel="stylesheet" type="text/css"/>
<link charset="utf-8" href="//search1.daumcdn.net/search/statics/common/css/2020/p_component.0205083152.css" rel="stylesheet" type="text/css"/>
<link charset="utf-8" href="//search1.daumcdn.net/search/statics/common/css/2020/p_collection.0401101130.css" rel="stylesheet" type="text/css"/>
<link charset="utf-8" href="//search1.daumcdn.net/search/statics/common/css/2020/p_collection2.0205083152.css" rel="stylesheet" type="text/css"/>
<link charset="utf-8" href="https://search1.daumcdn.net/search/statics/common/css/2020/p_shoppingColl.0129091048.css" rel="stylesheet" type="text/css"/>
<script type="text/javascript">
....
```



이 정보는 다음 포털 사이트에서 아이유를 검색해서 얻은 결과를 html로 표현한 것이다.

| ![image](https://drive.google.com/uc?export=view&id=1TqTqspoMV_aThcFxEU2mhRKsX3MiUyaY){:width="50%"} |
| :----------------------------------------------------------: |
| 아이유의 앨범 리스트를 볼 수 있는 페이지로 가려면 가수이름을 클릭한다. |

여기서 아이유의 세부 정보를 위해서 위 파란 사각형의 아이유 링크의 url을 가져온다.

```python
url2 = soup.select_one("#profColl > div.coll_cont > div.rwd_info.type_longtit3 > div > div.info_tit > a ").attrs['href']
```

Chrome을 기준으로 아래와 같이 선택하면  `#profColl > div.coll_cont > div.rwd_info.type_longtit3 > div > div.info_tit > a ` 가 선택된다. 

| ![image](https://drive.google.com/uc?export=view&id=1dfaYPz5KVbJrFSKJQp4r7ES-ZMgkLPLe){:width="70%"} |
| :----------------------------------------------------------: |
|                     링크 url을 얻는 방법                     |

# Selenium

`url2` 의 링크로 접속한 페이지를 `BeautifulSoup`으로 컨텐츠를 가져오면 앨범 페이지를 불러올 수 없다. 앨범 부분은 자바스크립트로 불러들여 결과 페이지를 확인해서 앨범명을 가져 올 필요가 있다. 결과 페이지를 부르기 위해서 `Selenium`을 사용했다.

우선 `webdriver`로 Chrome을 부르기 위해서 `chromedriver`를 다운로드 한다. 내 컴퓨터는 `Version 80.0.3987.132` 의 Chrome이 설치되어 있어 이에 맞는 `chromedriver`를 다운로드한다.

| ![image](https://drive.google.com/uc?export=view&id=1NyVVigsjapYPkvLxySTsdNddaCN3Aiq6){:width="70%"} |
| :----------------------------------------------------------: |
|                    chromedriver 다운로드                     |

**작성 중인 파일과 같은 위치에 두고** 아래 명령어로 호출한다.

```python
path = "./chromedriver"
driver = webdriver.Chrome(path)
```

그러면 `Selenium`로 작동가능한 Chrome 새 창이 생성되고, 이 창에 `url2`를 부른다.

```python
driver.get(url2)
time.sleep(5)
htmlSource = driver.page_source
driver.quit()
```

완전히 불러진 창을 보기 위해서 5초간 딜레이를 주고, 이제 페이지의 내용을 `htmlSource`에 저장한다.

새로 띄운 Chrome 창은 `driver.quit()`으로 닫을 수 있다.

`htmlSource`를 확인해보면 다시 unicode로 생성되어 있어 다시 `BeautifulSoup`의 `html.parser`를 사용하여 html 양식으로 바꿔 `soup2`에 저장한다.

```python
soup2 = bsoup(htmlSource, 'html.parser')
```



여기서 약간의 트릭이 있다. 앨범명을 가져오는데 앨범의 썸네일의 하이퍼링크 주소에 `album`이 들어가는 것을 확인하여 이를 이용해 앨범명을 가져왔다. 

```python
albums=[link.get_text() for link in soup2.select_one("#mArticle > div > div.info_cont.info_details ").find_all('a') 
 if 'album' in link.attrs['href'] and link.get_text() != '' and link.get_text() != '앨범']
```



마지막으로 `album` 변수를 확인하면 모든 앨범명을 가져온 것을 확인 할 수 있다.

```python
['사랑의 불시착 OST',
 '사랑의 불시착 OST Part 11',
 '첫 겨울이니까',
 'Love poem',
 '삐삐',
 '꽃갈피 둘',
 'Palette',
 '사랑이 잘',
 '밤편지',
 'CHAT-SHIRE',
 '마음',
 '소격동',
 'HIGH4 1st Mini Album `HI HIGH`',
 '애타는 마음',
 '꽃갈피',
 '봄 사랑 벚꽃 말고',
 'Modern Times - Epilogue',
 'Modern Times',
 'LOEN TREE Summer Story',
 '스무 살의 봄',
 'Last Fantasy',
 'Very Best Telecinema7',
 '얼음꽃',
 '최고의 사랑 OST',
 '최고의 사랑 OST Part.4',
 'Real+',
 '드림하이 OST',
 '드림하이 OST Part.1',
 'Real',
 'Road For Hope `선물`',
 '그대네요',
 '로드 넘버원 OST',
 '사랑의 리퀘스트',
 '로드 넘버원 OST Part.3',
 '잔소리',
 '텔레시네마 프로젝트 Vol.6',
 '첫사랑이죠',
 '낙원 OST',
 'IU...IM',
 '선덕여왕 OST',
 '음악여행 라라라 Live Vol.5',
 '2009 외인구단 OST',
 'Growing Up',
 'Lost And Found',
 '2012 MelOn Music Awards Nominees',
 '2013 MelOn Music Awards Nominees']
```



# Comments

꿀잼.