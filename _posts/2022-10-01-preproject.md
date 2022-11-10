---
layout: post
title: "Preparing Project"
subtitle: 웹
categories: project
tags: [web]
---
# 프로젝트 준비
최종 프로젝트를 위한 기술 스택 공부, 준비를 하려고 이번 포스트를 작성했다.
### 프로젝트와 어플리케이션
프로젝트 = 어플리케이션의 집합\
프로젝트에는 여러 앱이 포함될 수 있고 앱 또한 여러 프로젝트에 있을 수 있다.\
어플리케이션은 요청한 페이지를 보여주는 역할을 담당, 일반적으로 앱은 하나의 역할, 기능 단위로 작성한다.
## 가상환경 설정
프로젝트 조원들은 모두 같은 컴퓨터를 사용하지 않는다. 그러므로 비슷한 환경을 조성하기 위해 환경을 통일해주어야만 한다.

### 파이썬 가상환경 생성 명령어

현재 위치에서 venv라는 폴더를 생성하고 그 아래에 가상 환경을 설정하기.
```bash
python -m venv <venv>
```
### 가상환경 실행하기
윈도우 상황에서 실행하므로 아래와 같은 명령어를 실행한다.
```bash
가상환경생성폴더/venv/Scripts/Activate.ps1
```
### 가상환경 종료하기
```bash
deactivate
```
### 가상환경 pip 목록 저장
가상환경을 생성했다고 정리가 되는 것은 아니다. 매번 환경이 초기화 되므로 가상환경에서 사용한 패키지 목록을 저장을 해야한다. 

또한 팀원들과의 환경설정을 공유하기 위한 목적 또한 있다.
```bash
pip freeze > requirements.txt
```
### 가상환경 pip 목록대로 설치하기
가상환경 패키지 목록을 받고 그대로 적용하고 싶다면 아래와 같은 명령어를 실행한다.
```python
pip install -r requirements.txt
```

## WEB이란
기본적인 개념을 정리해보자.

HTTP 프로토콜이 서버와 클라이언트간의 데이터 전송을 관리
URL을 통해 클라이언트는 웹 요소에 접근
HTML 같은 웹 문서를 작성

- HTTP : HTML 문서 같은 리소스들을 가져올 수 있도록 하는 프로토콜
    - 웹에서 이뤄지는 모든 데이터 교환의 기초, 클라이언트-서버 프로토콜
- 요청 : 일반적으로 브라우저인 클라이언트에게 전송되는 메세지
    > 대표적인 클라이언트 프로그램 : 브라우저
- 응답 : 서버에서 응답으로 전송되는 메세지
    > 대표적인 서버 프로그램 : naver, google, github.. 

### CDN
Content Delivery(Distribution) Network

- 컨텐츠를 효율적으로 전달하기 위해
- 서버와 사용자 사이의 물리적 거리를 줄여 컨텐츠 로드 지연을 최소화
- 분산된 서버로 이루어진 플랫폼

장점
- 사용자와 가까운 서버를 통해 빠르게 전달 가능
- 외부 서버를 활용함으로써 본인 서버의 부하가 적어진다.
### 정적 웹페이지
서버에서 미리 저장된 파일이 사용자에게 그대로 전달되는 웹페이지\
서버가 정적 웹 페이지에 대한 요청을 받을 경우 추가 작업 없이 응답.
flat page 라고도 함.
### 동적 웹페이지
서버가 정적 웹 페이지에 대한 요청을 받을 경우 추가 작업을 통해 클라이언트에게 응답\
웹 페이지는 그때 그때 다르고 서버 사이드 프로그래밍 언어가 사용되고 파일을 처리, 데이터베이스와 상호 작용이 이루어진다.
## Django
### Framework
프로그래밍에서 특정 운영 체제를 위한 응용 프로그램 표준 구조를 구현하는 클래스와 라이브러리 모임\
재사용할 수 있는 수많은 코드를 프레임워크로 통합함.. 개발자가 새로운 애플리케이션을 위한 표준 코드를 작성하지 않아도 된다.
### Framework Architecture
MVC 디자인 패턴\
소프트웨어 공학에서 사용되는 디자인 패턴.\
사용자 인터페이스로부터 프로그램 Logic을 분리\
시각적인 요소를 쉽게 고칠 수 있는 어플리케이션을 만들 수 있다.\
`Django`는 `MTV Pattern`이라고 한다.
### 기본 구조
Model : 응용프로그램의 데이터 구조를 정의, 데이터베이스의 기록을 관리\
Template: 
- 파일의 구조나 레이아웃을 정의\
- 실제 내용을 보여주는데 사용

View :  
- HTTP 요청을 수신하고 HTTP 응답을 반환\
- Model을 통해 요청을 충족시키는데 필요한 데이터에 접근\
- template에게 응답의 서식 설정을 맡김

Templates : 
- 실제 내용을 보여주는데 사용되는 파일
- 파일의 구조나 레이아웃을 정의
- app폴더 안의 templates 폴더로 지정
### Django 구조 확인
init : Python에게 이 디렉토리를 하나의 Python 패키지로 다루도록 지시\
asig : 비동기 서버 게이트웨이 인터페이스, 장고가 비동기식이며 이 파일로 웹 서버와 연결 및 소통하는 것을 돕는다.\
settings : 어플리케이션의 모든 설정을 포함\
앱을 등록하기 위해서는 `INSTALLED_APPS`에 등록해야한다.

        반드시 생성을 하고 등록을 해야한다.

언어, 시간 설정 
```python
LANGUAGE_CODE = 'ko-kr'
# USE_I18N이 활성화되어 있어야 함

TIME_ZONE = 'Asia/Seoul'
# 데이터베이스 연결의 시간대를 나타내는 문자열 지정
# USE_TZ = False이면 Error 발생
```

urls : 사이트 url과 적절한 view의 연결 지정\
wsgi : Web Server Gateway Interface, django 어플리케이션이 웹 서버와 소통하는 것을 돕는다\
manage : Django 프로젝트와 상호작용하는 CLI 유틸리티\

참고 : 이러한 에러가 떴을 때..
```
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1030, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1007, in _find_and_load
  File "<frozen importlib._bootstrap>", line 984, in _find_and_load_unlocked
ModuleNotFoundError: No module named 'djangorestframework'
```
includes 에서 'djangorestframework'가 아닌 'rest_framework'로 바꿔준다.\
프로젝트 생성하기

```bash
django-admin startproject 프로젝트 명
```
앱 생성하기
```bash
python manage.py startapp 앱명
```

### Django template system
1. 표현과 로직을 분리한다.
    - 템플릿 시스템은 표현을 제어하는 도구, 표현 관련 로직
    - 기본 목표를 넘어서는 기능은 지원하지 말아야 한다.
2. 중복을 배제
    - 대다수의 동적 웹사이트는 공통 디자인을 갖는다.
    - Django 템플릿 시스템은 이러한 요소를 한곳에 쉽게 저장해, 중복 코드를 없애야 하며 이것이 템플릿 상속의 기초이다.
#### HTTP
추후에 기술할 프로토콜이지만 간단하게 짚고 넘어가자 \
웹에서 이루어지는 모든 데이터 교환의 기초를 의미한다.\
주어진 리소스가 수행할 원칙을 request method로 정의한다.\
1. GET
    - 서버로부터 정보를 조회할때 사용한다.
    - body가 아닌 Query String Parameters로 전송한다.

### Django URL
URL Path Converters 
> Variable Routing : URL 주소를 변수로 사용하기

URL의 일부를 변수로 지정, view 함수의 인자로 넘긴다.
- str
    - '/'를 제외하고 모든 문자열과 매치
    - 작성하지 않을 경우 기본값
- int
    - 0 또는 양의 정수와 매치
- slug
    - ASCII 문자 또는 숫자, 하이픈, 밑줄로 구성된 슬러그 문자열
- uuid
- path

일반적으로 하나의 urls.py에서 모든 view 함수를 관리하는 것은 프로젝트 유지보수에 좋지 않다.\
상위 디렉토리

```python
from django.urls import path, include
urlpatterns = [
    path('pages/', include('pages.urls')),
]
```
하위 디렉토리 상황
```python
# articles
urlpatterns = [
    path('index/', views.index, name='index'),
    ...
# pages
urlpatterns=[
    path('index/', views.index, name='index'),
    ...
```
url tag가 둘다 index로 동일하다.\
이러한 상황이면 서로 다른 index 이지만 같은 객체를 가르키게 된다.\
### URL namespace
URL namespace를 통해서 서로 다른 앱에서 동일한 URL이름을 사용할 수 있다.\
app_name을 지정한다.
```python
app_name = "pages"
urlpatterns=[
    path('index/', views.index, name='index')    
...
```
### Django Model
model(스키마 작성법)
```python
class Article(models.Model):
    title = models.CharField(max_length=10)
    content = models.TextField()
```
CharField -> 길이 제한이 있는 문자열을 넣을때\
max_length는 필수 인자\
TextField  -> 글자 수가 많을 때 사용