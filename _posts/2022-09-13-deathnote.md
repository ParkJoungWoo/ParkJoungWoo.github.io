---
layout: post
title: "데이터 분석 데스 노트"
subtitle: 데이터 분석
categories: project
tags: [data analysis]
---

## 1주차 복습


### 파일 읽기(txt)
```python
fd = open("파일명.확장자")
fd.readline()#여기서는 맨 윗 줄이 카테고리이기 때문에 읽고 버린다.
lines = fd.readlines() 읽은 내용을 lines에 저장
#lines는 list이며 
#문자열로 , 를 기준으로 나누어지고 마지막에 개행문자가 있다(txt 였기 때문)
['김길동,남자,23,3,유,O,165.3,68.2,02\n', 
 '이미린,여자,22,2,무,AB,170.1,53,03\n',
 '홍길동,남자,24,4,무,B,175,80.1,01\n', 
 '김철수,남자,23,3,무,AB,182.1,85.7,02\n']

fields = lines[0].split(",") 
fields라는 리스트가 아닌 단일 변수들로만 사용할 수 도 있다.
name, gender, age, money, fl ... = lines[0].split(",")
자동으로 변수들에게 나뉘어 저장

split 함수를 통해 문자열을 구분자로 나눌 수 있다

마지막 항목은 2.02\n으로 끝나게 된다... 개행문자를 지우기 위해서는
strip()함수를 통해 개행 문자를 지울 수 있다.
ex) lines[0].strip()

딕셔너리 에서 key와 item을 얻는 방법
my_dict = {'a':'b', "c", "d"}
my_dict.items() -> 원소 조회
for key, value in my_dict.items():
    key, value에 접근
```


```python
sort() 함수
"원본을 변형시켜 정렬한다."
알파벳은 사전순, 숫자는 오름차순
list.sort()

sorted() 함수
"원본을 변형시키지 않고 정렬한다"
list.sorted() <- 이것으로 정렬한 값을 조회 가능
list <-정렬 안됨

list.sort, sorted(reverse=True)
"원본을 역순으로 정렬한다"

sort, sorted(리스트, key=len)
"key를 길이로 보아 정렬한다. "

sort, sorted(리스트, key=lambda x: x[1])
"key를 x의 두번째 원소로 보고 정렬한다."
이게 무슨 말이냐면 본디 정렬은 단일 개체 list일 경우 정렬하는 기준이 명확해
sort를 쓰면 되지만 만약 항목이 여러개이고 원하는 정렬 기준이 달라지면
정렬하는 기준을 명시해야 하는데 이를 lambda함수를 통해 어떤 원소를 기준으로
할지 알려 준다.
sort, sorted(딕셔너리, key=lambda x: x[1])
"key를 x의 value를 기준으로 정렬한다"
만약 x[1]이 아닌 x[0], x[:]라면 key를 기준으로 정렬한다.


```

## 2주차 복습


```python
numpy활용법
import numpy as np
num = np.arange(30).reshape(2,3,5)
-> 넘파이 0~29의 숫자 리스트를 5개씩 하나의 리스트로 
그 리스트를 3개씩 하나의 리스트로
그 리스트를 2개씩 하나의 리스트로 만든다.

num.shape
(2,3,5)
num.ndim
3(3차원이니까)
num.size
30(30개의 숫자가 있으니까)

np.array(리스트)
리스트를 넘파이 배열로 
np.zeros((행, 열))
ex) np.zeros((3,4)) =
array([[0., 0., 0., 0.],
       [0., 0., 0., 0.],
       [0., 0., 0., 0.]])
np.arange(사이즈)
array([0 ... 사이즈-1])

np.arange(시작, 끝)
array([시작, 끝-1])

np.arange(시작, 끝, 증분)
array([시작, 시작+증분, 시작+증분+증분 ,,, 끝보다 작고 증분의 최대])

numpy 연산
A = np.array( [[1,2], [3,4]])
B = np.array ( [[2,3], [4,5]])

A*4
array([[ 4,  8], [12, 16]])
A+=3
array([[4, 5], [6, 7]])
np.sqrt(A)
array([[2.        , 2.23606798], [2.44948974, 2.64575131]])
A*B
array([[ 8, 15], [24, 35]]) ->
[[4, 5],  [[2,3],
 [6,7]] *[4,5]] (행렬의곱)
A@B
array([[28, 37],
       [40, 53]]) (내적)
```


```python
random
randn(모양) : 정규분포
    -1..1사이의 값을 모양 대로 array로 반환
randint(시작, 끝, size=3) -> 랜덤 정수
시작 ~ 끝-1 값을  3개 array로 반환
randint(시작, 끝, size=30).reshape(2,3,5)로 변경할 수 있다.

넘파이 배열을 A라고 둘때
A의 총합 A.sum()
A의 최소값 A.min()
A의 최대값 A.max()

A = 
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
라고 할 때(3,4)
A.sum(axis=0) ->첫번째 차원으로 합
A.sum(axis=1) ->두번째 차원으로 합
axis는 차원을 없애준다는 느낌으로 접근해보자
A같은 경우에는 3 * 4인데
axis = 0이면 null * 4
axis = 1이면 3 * null

3차원일경우(3 * 5* 10)
axis=0 null * 5 * 10
axis=1 3 * null * 10
axis=2 3 * 5 * null

이러한 느낌으로 접근하자.
```


```python
indexing, slicing
A=np.arange(5,15,1)
5 부터 14까지
A[2] => 7
A[(2,4),] => 7, 9 #소괄호에 인덱스들을 넣는다
A[2,4] -> (2,3)에 위치한 원소

A[:, ::2] => A의 모든 원소 중에서 열이 ::(0)에서 부터 2씩 증가해 
마지막 까지 달성하는 원소들

```


```python
DataFrame : 2차원 구조, column마다 다른 데이터 타입을 가짐
row에는 레코드, col에는 필드를 가진다.
데이터 처리, 통계 등은 column 단위로 이루어 진다.
csv 데이터 구조 처리에 특화

numpy는 array, list를 통해 생성된다면
dataframe은 dictionary를 통해 생성
dic= { 'gender' : [ 1, 2, 1,2], 'bloodtype': ["A", "B", "O", "AB"]}
dataF = pandas.DataFrame(dic)

문자열을 list로 저장하면 각 문자가 하나씩 저장된다.
ex) print(list("ABCD")) -> ['A', 'B', 'C', 'D']

DataFrame은 numpy array를 통해서도 생성이 가능하다.
pandas.DataFrame(np.random.rand(6,4), index=np.arange(6), columns=list("ABCD"))
DataFrame(array, index=array, columns=array)로 초기화 가능
index(row)는 왼쪽에 columns는 위쪽에
-> 입력받는 array랑 index, columns의 크기가 같아야 성립.

날짜 관련
dates = pandas.date_range("20220103", periods=6)
2022-01-03 을 시작점으로 총 6일의 날짜를 출력
2022-01-03 ... 2022-01-08

df.head()
df.tail() -> 최초, 마지막 5개씩 출력
df.head(n)
df.tail(n) -> 최초, 마지막 n개씩 출력
df.index : df의 index(row) 확인 가능
df.columns : df의 column확인 가능
    
df.describe() df의 여러정보확인가능
(count, mean, std, min, 25%, 50%, 75%, max)

df.sort_values(by="B")
df.sort_values(by="B", ascending=False) => 내림차순
by="Key"이기 때문에 오직 하나만 가능하다.
"B"는 columns을 의미, 
	A	B	C	D
2021-03-13	0.564162	-1.345139	1.205831	1.282519
2021-03-12	-1.592712	-0.707864	-0.009921	-0.615554
2021-03-11	0.443845	-0.091939	-1.476495	1.160637
2021-03-14	0.297359	0.265152	-0.130253	0.374368
2021-03-10	0.013351	0.476958	0.057244	-0.874045
2021-03-09	0.541684	1.347803	0.647960	-0.049265
==다른 값들은 상관 없고 B에 대해서 정렬을 한다(오름차순)
당연한 말이지만 이에 따라서 다른 원소들은 B에 맞춰서 자리가 정해진다.
03-13의 B가 가장 작은 B이니 제일 위에 위치(나머지 A, C, D도 불가항적이게 위치)

```


```python
Dataframe의 인덱싱, 슬라이싱
df[0:3] -> DataFrame의 첫번째 원소 부터 2번째 원소까지 모두 리턴
df[["age", "grade"]] -> DataFrame의 "age", "grade" column의 모든 원소를 리턴
df.iloc[1:5, 0:3] -> DataFrame을 숫자로 접근(row는 1:5, column은 0:3으로)
df.iloc[[1,2,4], [0,1,2]]F -> 슬라이싱 되니까 배열로도 가능하다.
df.loc[[1,2,4], ["age", "grade"]]  -> row는 1,2,4, column은 age, grade를 
df.loc[1] -> row가 1인 원소의 데이터
만족하는 원소들만 리턴

평균을 만드는 방법
np.mean(df.loc[:, ["age"]])
df.loc[:, "age"].mean()

DataFrame을 pandas로 연결하기
df2 = df[1:3] 1~2의 데이터
df3 = df[5:8] 5~7의 데이터
df4 = pd.concat([df2, df3], axis=1) 1,2,5,6,7의 데이터로 연결해서 DataFrame만들기
질문 1) 왜 axis=0은 안되는 걸까? -> 아래의 axis 개념을 보고 생각해 본다면 
axis=0은 가장 고차원에서 본다는 뜻 그 즉슨 데이터 [] + 데이터 [] 의 의미라는 뜻이라
목적은 다른 컬럼의 데이터를 하나로 합치고 싶음(A의 나이, 학점과 A의 키, 무게를 하나로 ,,, 나이학점 따로
키, 무게를 따로 합치고 싶은 의도가 아님, A의 나이 학점 키 무게를 한번에 저장하고 싶음)
그렇기 때문에 가장 낮은 차원인 axis=1에서 데이터를 concat하는 것이다.
```

### ******************세상 중요 **************************


```python
DataFrame의 조건에 맞는 원소 접근
df[ df["bloodtype"]=="B"]
데이터프레임[데이터프레임[컬럼] 조건식] 
-> 모든 데이터에서 조건식을 만족하는 원소들만 출력

여러 조건식 예시
df[df["bloodtype"] == "B"] -> 혈액형이 B인 원소들의 레코드 출력
df.loc[df["bloodtype"].isin({["A", "B"]) , ["name", "age"]]
-> A, B형 인 원소들의 레코드 중에서 이름과 나이만 출력
                             
축 개념 이해하기
[[[1,2],[3,4]],[[5,6],[7,8]]] 의 배열을 생각해보자
axis = 0 가장 고차원에서 보기 [[1,2],[3,4]] -> [[5,6],[7,8]]
axis = 1 [1,2] -> [3,4] 또는 [5,6] -> [7,8]
axis = 2 1->2 또는 3->4 또는 5->6 또는 7->8
```


```python
DataFrame의 컬럼 추가하기
df["새로 넣고싶은 컬럼"] = 원 하는 데이터
DataFrame 에서는... df["새로운 컬럼"] = df["어떠한 컬럼"] (연산) df["다른 컬럼"]

python 에서 제곱 
a**2 세제곱 a**3 ....

Pandas로 데이터 읽기
dff= pd.read_csv("footsize.csv", encoding="cp949")

병합
df3= pd.merge(df, dff, left_on='name', right_on='realname')
merge (A, B, on=키 ) : 기본은 inner join
    
데이터 프레임의 데이터 정렬 방법 -> groupby
df.groupby("grade").mean() -> 데이터 프레임을 grade로 정렬하고 그것의 평균을 구한다.

df.groupby(["sex","bloodtype"]).mean() // 여러 컬럼을 사용할 수 있다.
size() -> 크기 출력
```


```python
Matplot
import matplotlib.pyplot as plt
plt.plot([1,2,3,4])
x는 자동으로 0,1,2,3으로, y는 temp[0], temp[1]...
plt.plot([1, 2, 3, 4], [1, 4, 2, 3])
plt.plot(A, B) A가 x, B가 y

input 데이터는 numpy 배열이나 pandas column이 가능하다

plot의 모양은 
plot type
"ro" red circle
"b-" solid blue line(디폴트)
"r--" red dasesh
"bs" blue squares
"g^" green trianles

plt.axis([xmin, xmax, ymin, ymax]) -> 축의 범위를 지정

하나의 plot에 여러 그래프를 같이 그리기
plt.plot(t, t, 'r--', t, t**2, 'bs', t, t**3, 'g^')
x,y,표시방법,x1,y1,표시방법 ,,,,으로 plot을 만들고
x를 고정하든 y를 고정하든 방법은 많다.
기준 중 가장 큰 것을 plt의 기준으로 한다.

차트에는 여러가지 종류가 존재(bar, scatter, plot)
막대 그래프를 의도할 경우
plt.bar(X, Y)
산점도 그래프를 의도할 경우
plt.scatter(X, Y)
```


```python
하나의 그림에 여러 그래프를 그리고 싶을 경우
subplot()을 이용한다.
x1 = np.linspace(0.0, 5.0)
linspace -> linspace(시작, 끝, 개수) 시작, 끝 까지의 개수 만큼의 리스트(끝 포함)
linsapce(시작, 끝) -> 시작, 끝까지의 50개의 일정한 차이의 리스트(끝 포함)
x2 = np.linspace(0.0, 2.0)

y1 = np.cos(2 * np.pi * x1) * np.exp(-x1)
y2 = np.cos(2 * np.pi * x2)

plt.subplot(2, 1, 1) 2*1의 서브 플롯 중에서 첫번째에 위치시키기
plt.plot(x1, y1, 'o-')
plt.title('1st Graph')
plt.ylabel('Damped oscillation')

plt.subplot(2, 1, 2) 2*1의 서브 플롯 중에서 두번째에 위치시키기
plt.plot(x2, y2, '.-')
plt.title('2nd Graph')
plt.xlabel('time (s)')
plt.ylabel('Undamped')

plt.tight_layout()#예쁘게 나타내기 위해서
plt.show()

plt.bar(["A","B","AB","O"], df.groupby(["bloodtype"]).size()) -> groupby한 데이터를 size()로 표시한 것을
x의 컬럼 순서대로 나타낸다.
df.groupby(["bloodtype"]).size().plot.bar() // dataframe에 있는 plt가 맞는건가.../ plt와 연계된다
(subplot으로 구별 가능)

여러 정보를 하나의 그림에 표시하기
plt.scatter("height", "weight", c="grade" ,data=df)
해석 : x축은 height로 y축은 weight로 해서 데이터를 표시하고 
그 데이터를 색깔로(기준은 grade) 또다시 구분한다.

```

크롤링 종류
스크레이핑 : 인간 사용자를 위해 제공된 웹페이지
API 기반 정보 수집 : 프로그램을 위한 정보 제공 API가 존재

Facebook API 활용 : OAuth - 특정 사용자의 동의를 얻어 상용자의 정보를 가져오는데 주로 활용
![one](/assets/img/0913/facebook.png)
개발 흐름은 user_token(동의)를 확보하고 api에 user_token 포함하여 호출한다.


```python
크롤링 하기
Beautifulsoup를 활용한다.
soup = BeautifulSoup(html형식데이터, 'html.parser') #파싱하기
soup #파싱한 객체
prettyfy() 파싱된 객체 구조를 예쁘게 보여준다.

예제)
from bs4 import BeautifulSoup 
html= '''
<h1 id="title">숭실대학교</h1>
<div class="top">
<ul class="menu">  <li> <a href=http://www.ssu.ac.kr/member/login.html class="login">로그인 </a></li></ul>
<ul class="dept"> 
    <li><a href="http://www.ssu.ac.kr/sw/">소프트웨어학부</a></li>
    <li><a href="http://www.ssu.ac.kr/ai/">AI융합학부</a></li>
    <li><a href="http://www.ssu.ac.kr/stat/">정보통계학부</a></li>
</ul>
</div>
'''
soup=BeautifulSoup (html, 'html.parser')

## 첫번째 아이템을 보여준다
soup.h1 -> 파싱한 객체에서 h1 컴포넌트를 가져온다
soup.ul -> 파싱한 객체에서 ul 컴포넌트를 가져온다
soup.li -> 파싱한 객체에서 li 컴포넌트를 가져온다

text = soup.find(string="소프트웨어학부") text찾기 // text.string -> "소프트웨어학부"


## 검색한 모든 아이템을 보여준다 (리스트 형태로 보여줌)
soup.find_all("ul")
soup.find_all("li")
soup.find_all(id="title") id가 title인 모든 정보를 보여줌
soup.find_all(attrs={'class':'dept'}) class가 dept인 데이터를 검색한다
soup.find_all(['ul', 'li']) -> 여러 속성을 검색할 수 있다.
soup.find_all(string =re.compile("학부")) -> 끝이  "학부"로 끝나는 모든 데이터를 검색한다.(list로 리턴)

응용 -> all = soup.find_all('a')
for i in all:
    i.string
-> soup객체에서 a태그를 가지고 있는 모든 데이터의 string을 출력




#태그로 검색하기
tag_a = soup.a -> tag_a에 soup의 a 속성을 찾은 결과를 저장한다.

tag_a.attrs -> a tag의 모든 정보를 보여준다.
tag_a['href'] -> a tag의 'href' 정보를 보여준다
```

## 데이터 통계⭐


```python
테스트용 데이터 
import seaborn as sns
titanic = sns.load_dataset("titanic")
또는 읽어 오기
titanic.to_csv("titanic.csv", index=False) 
#데이터의 기본 정보 보기
titanic.info()
titanic.describe() -> 일반적인 정보 확인
#데이터 실제 데이터 보기
titanic.head()
titanic.tail()
#데이터의 타입 확인하기
titanic['age'].dtype -> titanic의 ["age"]의 dtype으로 접근 가능
titanic.dtypes -> titanic의 타입들 모두 출력
dic = dict(titanic.dtypes)로 딕셔너리로 만들 수 있다.

타입이 검사하고 싶은 타입과 같은지 확인 하고 싶을 때
titanic['survived'].dtype  == np.int64

for c in titanic.columns : -> titanic.columns로 모든 컬럼에 접근이 가능하다
    print ( titanic[c].dtype == np.int64) 위에서 언급한 타입 검사를 위한 체크
    
객체의 type을 변환시키고 싶다면
titanic['survived'].astype(float) -> 또는 'float'
물론 타입 변환은 가능 한 데이터 (숫자에서 숫자)만 가능하고
만약 NaN값이나 inf 값이 있다면 불가능 하다.
```


```python
통계의 기본 -> nominal
도수(개수)
titanic.age.value_counts() 
데이터프레임.컬럼.value_counts()
또는
titanic['age'].value_counts()

비율로 나타내기
titanic.age.value_counts(normalize=True)
titanic['age]'].value_counts(normalize=True)

의미상 nominal -> int, object, category, bool 이라고 할 수 있나?

plot으로 그려보기
막대 그래프
titanic.survived.value_counts().plot(kind='bar')
titanic.survived.value_counts().plot.bar()
원형그래프
titanic.survived.value_counts().plot(kind='pie')
titanic.survived.value_counts().plot.pie()

seaborn에서 제공하는 plot 사용하기
sns.countplot(x='survived', data=titanic)
seaborn에서 제공하는 도수 그래프 x또는 y(둘 중 하나)는 컬럼, data는 대상 데이터

가장 기본적인 분석 -> 범위, 최대, 최소, 평균 ... describe()로 볼 수 있음

numpy에서 제공하는 평균, 중간값, Quartile값
np.mean([1,2,3,4,5])
np.median([1,2,3,4,5])
np.median([1,2,3,4,5,6]) -> 짝수개이면 중간 두개의 평균값
넘파이 배열로는 quantile을 사용 불가
DF로 사용한다고 일단 알자
quantile사용법
titanic['age'].quantile(0.5)
titanic.age.quantile(0.5)
-> titanic의 age 컬럼에서 50%에 해당하는 값을 출력하기

titanic['age'].median()
titanic.age.median()
-> titanic의 age 컬럼에서 중간값에 해당하는 값을 출력하기

Quartile : 1/4 단위로 자른 0%, 25%, 50%, 75% , 100% 의 값
Quantile : 100% 중에 어느 값
IQR : 25~75% 의 범위 => 극단값에 영향을 받지 않는 중간 50% 의 범위를 의미
    
plt.boxplot(titanic.fare, vert=False) 값의 분포를 보기위한 방법(일자로 정렬)
vert를 True 로 하면 수직으로 보여주고 vert를 True로 하면 수평으로 보여준다
```


```python
BOXPLOT의 기본적인 정보

값이 다른 데이터 분포를 크게 벗어나는 데이터
평균 등에 큰 영향을 줌
처리상의 오류일 수도 있고, 진짜 큰 값일 수도 있다 (몸무게 300kg vs. 500kg, 연매출 1조 vs. 8000조 )
통계적 정의 ( by John Tukey)
25% 값 - 1.5 X IQR 보다 작은 값
75% 값 + 1.5 X IQR 보다 큰 값

IQR 구하기 -> 75% - 25%
```

numeric (continuous) data => 구간화 (bin) 해서 출력
countplot 으로 출력하는 경우 : 동일 값이 많이 나오면 그걸 보여주기는 하지만 어느 부분에 분포가 많이 되어 있는지와는 또 다르다
정수 데이터와 실수 데이터는 또 양상이 다르다

numeric(continuous) data(연속적인 분포를 가지는 데이터)
구간화를 통해서 출력한다
sns.countplot(x='fare', data=titanic)

![image-3.png](/assets/img/0913/one.png)
히스토그램 -> 도수 분포를 정보 그램으로 나타내기
sns.histplot(x='fare', data=titanic)

![image-2.png](/assets/img/0913/two.png)
bins의 범위를 정해서 출력이 가능하다.
sns.histplot(x='fare', data=titanic, bins=[0,100,200,300,400,500,600]) # 또는 bins= 갯수

![image.png](/assets/img/0913/three.png)

```python
결측치, 오류 데이터 처리
특이치 : 실제 데이터 값이 특이하게 크거나 작거나, 주로 numeric에서
원칙은 아웃라이어 기준으로 판단해야..
quantile (0.99) 값 등으로 대체 하기도 함
결측치 : 데이터 값이 없는 것 , NA라고도 함
해당 레코드를 삭제하거나
통계시 (통계란 column 단위로 이뤄짐) 해당값 빼고 계산
대체 값 (평균, 중간값, 임의값, random 값) 을 만들어 넣기
column 별로 통계를 내는 것이기 때문에 결측치가 있어도 상관없다. 엉뚱한 값을 억지로 넣는 것 보다는..
  - 다만, paired value analysis (같은 레코드의 두 값을 비교 ) 하는 경우는 결측치가 있으면 안됨
오류 : 쓰레기 값이 들어있는 경우
cleansing 이 필요: 원래 값을 살릴 수 있으면 살리고, 아니면 NA화 후 처리
whitespace제거 등등
같은 항목으로 count될 수 있게
혈액형 A, B, O, AB 만 있어야 <= category의 경우 프로그램적으로 거를 수 있다
결측치 (NA) 처리
결측치 찾기 : isnull()
값 넣기 : fillna ( 넣을값 )
결측치 포함 행 (레코드) 삭제 : dropna( subset=[체크할 컬럼명 ] )
```


```python
결측인 데이터 확인
titanic.isnull().sum()
결측 데이터 삭제하기
titanic=titanic.dropna(subset=['age'])
결측 데이터를 중간값으로 충당하기
titanic.age= titanic.age.fillna( titanic.age.median())

0.99 quantile 값보다 큰 값을 0.99quantile 값으로 교체하기
titanic['fare'][(titanic['fare'] > titanic['fare'].quantile(0.99))] = titanic['fare'].quantile(0.99)
-> DataFrame 문법 활용

Category 활용을 위한 설정
컬럼.unique() -> unique한 값 조회
pd.Categorical(컬럼, categories=[컬럼의 값1, 값2,,...]) 지정한 컬럼의 값을 제외하고는 모두 NaN처리
who의 데이터 출력(카테고리)
titanic.who.unique()

pd.Categorical(titanic.who, categories=["man", "woman"]).unique()
타이타닉의 성별 man, woman을 제외한 값을 NaN으로 처리한다.
```


```python
통계적 추정
빅데이터 : 전체 (투표) , 통계 : 샘플 (여론조사)
샘플 -> 전체를 추정 (여론 조사)
샘플통계 : 샘플 1000명을 조사 해본 결과 400명이 지지했다
점추정 : 전체 인구의 지지율도 40% 일 것이다
구간 추정
여론조사 결과 40% (95% 신뢰 구간에서 ±4% 오차)
= 모집단(전체 인구) 의 (실제)지지율은 95%의 확률로 40%±4% 인 36%~44% 사이에 있을 것이다.
= 36~44% 사이에 있을 확률이 95%
= 5%의 가능성으로 36% 보다 작거나 44% 보다 클 수 있다.

신뢰구간 구하기
import scipy.stats as st
30 개 이하의 샘플 : st.t.interval(alpha=신뢰구간, df=샘플 갯수-1, loc=평균, scale=평균오차)
30 개 이상 : st.norm.interval(alpha=0.95, loc=np.mean(data), scale=st.sem(data))
샘플 뽑기 : df.sample( n=샘플갯수 or frac= 샘플비율)
    
sample1=titanic.sample (n=30)
sample2=titanic.sample (frac=0.2)

print ("sample1:", np.mean(sample1.fare), st.t.interval(alpha=0.95, df=len(sample1)-1, loc=np.mean(sample1.fare), scale=st.sem(sample1.fare)))
print ("sample2:", np.mean(sample2.fare), st.norm.interval(alpha=0.95, loc=np.mean(sample1.fare), scale=st.sem(sample1.fare)))
print ("population:", np.mean(titanic.fare))
```


```python
평균비교와 도수 비교

평균비교

2개의 평균이 있을 때, 샘플에서는 평균이 차이가 나는데 실제 모집단에서도 차이가 날지 분석

one sample t-test : 1개의 기준값 (ex, 지난 선거에서 지지율) 이 주어져 있고, 샘플 데이터 (ex. 이번달 여론조사 데이터) 가 주어진 경우, 유의미한 차이가 있나?

가설 검정

가설 : 실제 모집단에서는 차이가 없다
검정 : 가설이 맞는지 판단하는 절차
예

키 크는 약이 효과가 있는지.. 약을 먹인 몇 명의 결과를 놓고 판단하고자 함
원래 중학교 2학년 인구의 키 평균 = 164
1학년부터 약먹은 2학년(샘플) 평균 = 167
영가설 : 원래 키에서 변동이 없다 (샘플에서만 평균이 높게 나왔을 뿐)
검정 : 샘플로 추정할 수 있는 모집단의 신뢰구간 (전체 중학생에게 약을 먹였을 때, 그 평균은 95%의 확률로 이 구간내에 있을 것이다) 안에 원래 평균(164) 이 있다면, 영가설이 맞다 (변동이 없다=효과가 없다)
만약 검정 결과 원래 평균이 신뢰 구간 밖이라면, 영가설 기각 (변동이 있다)
평균값이 => 통계적으로 유의미한 차이가 있다.
st.ttest_1samp(데이터, 비교평균)

pvalue < 0.05 이면 영가설 기각 (아니면 영가설이 맞음)

비교 코드
st.ttest_1samp (sample1.fare, 20) # 샘플 집단의 배 삯 평균은 20

도수비교

nominal 데이터의 값 별 분포가 차이가 있는가? (샘플에서 그렇게 나왔는데 전체도 그렇다고 추정할 수 있나?)
특정 혈액형이 더 많은가?
가설 : 혈액형 분포는 같다 = 고르게 분포한다 (영가설)
검정 : chisquare ( 데이터 )
실제 분포가 균등한데, 샘플이 이렇게 나올 확률을 얻음
if pvalue < 0.05 전체도 다르다고 추정할 수 있음
카테코리 별로 => 통계적으로 유의미한 차이가 있다.

비교 코드

from scipy.stats import chisquare
chisquare ([10, 12, 13, 5])  # 원래는 균등 분포인데, 샘플에서만 우연히 이렇게 차이나왔을 가능성 28%
# 샘플이 커지면 
chisquare ([100, 120, 130, 50 ] ) # 원래는 균등 분포인데, 이렇게 큰 샘플에서 우연히 이렇게 차이가 많이 나게 나올 가능성 0.000002 %
# titanic data에 적용 
num_class=sample1["class"].value_counts()
print (num_class)
print ( chisquare (num_class) )
num_class=sample2["class"].value_counts()
print (num_class)
print (chisquare (num_class))
#num_class
```

##5주차 복습

종속 변수와 독립변수에 대해서 생각해보자
독립변수가 종속변수에 영향을 주었는지에 관해서 고민
![image.png](/assets/img/0913/four.png)
셀수있는(셀수 없는 것을 정렬한), 셀수 없는(추상적인 셀수 있는것)
numeric과 numeric -> 상관분석(cor : Correlation Analysis) oR 회귀분석(lm : Regression Analysis)
평균비교
교차분석


```python
nominal - nominal(숫자가 아닌것 간의 관계) : 교차분석
ex) 성별에 따라 생존자 숫자가 다른가
pd.crosstab(변수1, 변수2, normalize=True 또는 False) : normalize = 비율로 보여줄지 말지 선택
예제 : 
titanic= sns.load_dataset("titanic")
pd.crosstab(titanic.sex, titanic.survived)

##crosstab을 활용해서 plot을 만들 수 있다
ct=pd.crosstab(titanic.sex, titanic.survived)
ct.plot.bar(stacked=True)   # seaborn lib
ct.plot.pie(subplots=True)
```


```python
차이의 통계적 유의성
통계적 유의성 (= 모집단에서도 그럴 것인가 = 지금 샘플의 결과에서의 차이가 통계적으로 유의미한 차이인가)
chi2_contingency(crosstab)
pvalue < 0.05 이면 (남녀 생존율이) 통계적으로 유의미한 차이가 있다.

from scipy import stats
#ct는 위에서 구한 교차 분석 결과
stats.chi2_contingency(ct) # 2번째 값이 p value
결과 :
    (260.71702016732104,
 1.1973570627755645e-58,
 1,
 array([[193.47474747, 355.52525253],
        [120.52525253, 221.47474747]]))
```


```python
nominal-numeric : 평균 비교
nominal data value group (독립변수) 에 따라 numeric value (종속변수) 의 mean이 다른가?
독립변수 -> 종속변수
df.groupby (독립변수).종속변수.mean()

#변수 둘
sns.boxplot(x='sex', y='fare', data=titanic)
#변수 셋
sns.boxplot(x='sex', y='fare', hue='class', data=titanic)
```


```python
2변수 평균 비교 - 통계적 유의성
Nominal인 독립변수 값의 종류가 2가지 일 때 : t-test

numeric 종속변수
3가지 이상일 때 : anova ( 3변수 이상과 구분 )
T-test도 2종류가 있음

독립적 2 sample 비교 : 서로 다른 group의 값을 비교하는 경우 (예: 남 vs.여)
paired 2 sample 비교 : 같은 대상의 서로 다른 값을 비교하는 경우 (예: 약먹기전의 수치 vs. 약먹은 후의 수치)
독립적 2 sample 비교

우선, 분산 비교(F-test: 등분산성, 분산이 차이가 있는지 없는지 )를 해야 함
levene ( sample1, sample2) : p-value < 0.05 이면, 통계적 유의성이 있게 분산의 차이가 있음
ttest_ind ( sample1, sample2, equal_var=True of False)
```


```python
from scipy.stats import levene, ttest_ind, ttest_rel

# 값이 2 종인 독립변수
titanic.groupby('sex').fare.mean() 
male= titanic[titanic.sex=="male"]
female= titanic[titanic.sex=="female"]
levene(male.fare,female.fare)
결과 : LeveneResult(statistic=19.188225990910503, pvalue=1.3255194429754062e-05)
ttest_ind(male.fare, female.fare, equal_var=False)
결과 : Ttest_indResult(statistic=-5.077499013458909, pvalue=5.388057409693912e-07)
```


```python
paired 2 sample
paired 2 sample : ttest_rel ( 벡터1, 벡터2)
벡터1, 2의 길이가 같아야..
같은 레코드 내의 2개 변수 pair
2개 변수는 같은 종류 (예, 몸무게)
약을 먹기 전 후에 몸무게 비교
NA 가 없어야
import numpy as np
sl=pd.read_csv("studentlist.csv",encoding="cp949")
sl.insert(7, column='height2', value=sl.height+np.random.randint(0,5, len(sl)))
ttest_rel ( sl.height, sl.height2)
```


```python
3가지 이상의 독립변수의 경우
titanic.groupby('pclass').fare.mean()
sns.boxplot(x='pclass', y='fare', data=titanic)
```


```python
one way anova
m=ols ('종속변수~독립변수', df).fit () : 모델 생성
anova_lm (m) : 모델에 대한 정보 조회
데이터분석에서 모델이란 : 간단히 데이터 또는 데이터 간의 관계를 설명하는 도구
    
from statsmodels.formula.api import ols
from statsmodels.stats.anova import anova_lm
model=ols('fare~pclass', titanic).fit()
print (anova_lm(model))
-해석 : class 별 차이가 통계적 유의성이 있는 수준이다.
```


```python
numeric-numeric (상관분석)
상관관계 : 같이 증가, 같이 감소 (선형적 상관관계)
독립변수 -> 종속 변수 같은 순서가 없음
데이터를 봐서는 상관관계를 알기 어려움, 시각화 필요
상관계수구하기: df.corr() <= 방향성이 없음
# 변수 간 상관계수 구하기 
titanic[['age','fare']].corr()

상관분석 : 상관도가 높은 변수 pair를 찾아내기
위의 table 도 눈에 안들어오니 시각화 필요
sns.pairplot(df, hue=키변수) , 키 변수 : 제3의 변수 추가 분석
    
pairplot, heatmap을 활용한다.
iris=sns.load_dataset('iris')
sns.pairplot(iris, hue = 'species')
sns.heatmap( titanic.corr())
```

discrete vs. continous
discrete vs. continous 과 nominal vs. numeric 에 대해 다시 생각
상관계수의 강도 보기 : heatmap(), 밝을 수록 높은 상관관계


```python
## numeric-numeric (회귀분석)
- 상관관계 : 2 변수가 같이 증가, 같이 감소 (선형적 상관관계, 직선에 얼마나 몰려있는지)
- 회귀분석 : 독립변수 -> 종속변수 선형적 상관관계의 정도(기울기)까지 분석
x=np.arange(0,10)
y2=x**3
stats.pearsonr(x,y2)
stats.spearmanr(x,y2)
```

스피어만 상관계수
기본은 pearson : 선형관계 (값의 크기 자체가 의미가 있는 numeric : 곱하기 나누기가 의미를 갖는 숫자)
수치형 변수 종류 (3가지)
순서만 의미있는 수치 : 학력 (고졸, 대졸, 대학원졸.. )
+/- 가 가능한 수치 : 섭씨온도, 지능지수
*/ / 가 의미있는 수치 : 중량
spearman: 값의 크기 대신 순위로 계산 (순서가 있는 nominal 데이터)

상관 분석 시 주의점
상관계수의 해석이 주관적임(다음 같은 기준이 있긴 하나..)
![image.png](/assets/img/0913/five.png)

특히,개별 관측치에 속한 2변수 (예, 키-몸무게) 가 아닌, 상이한 2개의 통계 데이터( 연도 별 키의 통계, 연도 별 몸무게의 통계) 의 상관관계는 큰 의미가 없다

30개 이상의 관측치(레코드)가 있어야 의미가 있다. t-test에서도 마찬가지..

회귀 분석 (regression)
단순 선형회귀 : 하나의 독립변수 -> 하나의 종속변수
모델 생성 : 모델=ols( '종속변수~독립변수', data=데이터).fit()
모델 정보 조회 : 모델.summary(), 모델.params()
    
from scipy import stats
from statsmodels.formula.api import ols, glm 

m1 = ols ( 'quality~alcohol', data=wine).fit()
m1.summary()
![ima](/assets/img/0913/six.png)
해석
coef : 선형 모델 (1차함수 직선)의 기울기 (선형적으로 어느 정도 영향을 주었는지 분석)
intercept: 절편
y(종속변수)= coef*x(독립변수) + intercept
시각화 plot_ccpr ( 모델, 독립변수)


```python
# 모델의 시각화
sns.regplot(x="alcohol", y="quality", data=wine)
모델과 예측
모델이란 ? 데이터를 설명하는 단순한 툴 (여기서는 1차함수의 절편, 기울기 => linear model)
모델을 이용해서 예측을 할 수 있다.

red_df= pd.read_csv("winequality-red.csv", sep=";")
red_df.head()
m1 = ols ( 'quality~alcohol', data=wine[:3000]).fit()
m1.predict( wine[3000:].alcohol)
```


```python
중선형 회귀
중선형회귀 : 독립변수가 여러개 일때
모델 생성 : 모델=ols( '종속변수~독립변수1+독립변수2+ ... ', data=데이터).fit()
#공백있는 column 이름 처리 
wine.columns = [c.replace(' ', '_') for c in wine.columns]


m2 = ols ( 'quality~fixed_acidity+volatile_acidity+citric_acid+residual_sugar', data=wine).fit()
m2.summary()
각 독립변수의 영향이 출력됨
p < 0.05 작은 경우만 유의성을 가짐
시각화 : plot_partregress_grid (모델)

#시각화 
from statsmodels.graphics.regressionplots 
import plot_partregress_grid
fig=plot_partregress_grid(m2)
fig.tight_layout(pad=1)
```
