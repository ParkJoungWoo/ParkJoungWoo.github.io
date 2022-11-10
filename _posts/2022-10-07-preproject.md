---
layout: post
title: "Preparing Project 2"
subtitle: 웹
categories: project
tags: [web]
---
## Migrations
Django가 model에 변화를 반영하는 방법.

- makemigrations
    - model을 변경한 것에 기반한 마이그레이션 만들때
- migrate
    - 마이그레이션을 DB에 반영하기
    - 설계도를 실제 DB에 반영
    - 모델 변경 사항과 DB의 스키마가 동기화
- sqlmigrate
    - 마이그레이션 SQL 구문을 보기 위해 사용
    - 마이그레이션이 SQL문으로 어떻게 동작하는지 미리확인
- showmigrations
    - 프로젝트 전체 마이그레이션 상태 확인
    - 마이그레이션 파일 migrate 여부 확인
```bash
마이그레이션 만들기
python manage.py makemigrations

실제 DB에 반영
python manage.py migrate

마이그레이션 SQL 구문 확인하기
python manage.py sqlmigrate 앱이름 0001 -> migration순서

프로젝트 전체 마이그레이션 상태 확인
python manage.py showmigrations
```
### migration 순서
model의 변경사항이 있다
1. models.py
2. python manage.py makemigrations
3. python manage.py migrate

```SQL
Article -> 클래스명

article = Article()
article.title = 'first'
article.content = 'django'
Article.objects.all()

article.save() -> 객체 저장

article.title ...'first'
article.content ... 'django'
article.created_at ... datetime.datetime(2019, 8, 21, 2 ...)
```
또는
```SQL
article = Article(title='second', content='django!!')
article.save()

article.pk -> 2 (django에서는 index가 pk)
article.title -> second

Article.objects.create(title='third', content='django!!')
-> article.save()를 건너뛰기
```

```python
class Article(models.Model):
    title = models.CharField(max_length=10)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    def __str__(self):
        return self.title
__str__을 통해 object가 사람이 읽을 수 있는 문자열 반환
```