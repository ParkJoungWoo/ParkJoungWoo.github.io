---
layout: post
title: "비지도학습"
subtitle: 머신러닝
categories: machine learning
tags: [ml]
---

# 비지도 학습과 지도 학습
지도 학습과 다르게 비지도 학습은 성능평가가 어렵다.

label이나 기준이 없기 때문

## Kmeans, DBSCAN
대표적인 비지도학습

`Kmeans`와 이를 보완한 `DBSCAN`이 존재한다.

## 한계점

iris데이터를 통해 비지도 클러스터링, 실제 값을 비교해보자.

```python
from sklearn.cluster import KMeans
import matplotlib.pyplot  as plt
import seaborn as sns
from sklearn import datasets
import pandas as pd
from pandas import DataFrame
from sklearn.datasets import load_iris

iris = load_iris()
kmeans = KMeans(n_clusters=3, max_iter=500) #센트로이드가 이동하는 횟수

cluster = kmeans.fit_transform(iris.data)

plt.figure(figsize=(12,6))
# 군집별로 어떻게 분류했는가
plt.subplot(1,2,1)
sns.countplot(kmeans.labels_)
plt.title("KMeans Clustering", fontsize=15)

# 실제
plt.subplot(1,2,2)
sns.countplot(iris['target'])
plt.title("Original", fontsize=15)

plt.show()
```
![count](/assets/img/0922/count.png)

실제 분류보다 잘 못한 것을 알 수 있다.

이는 K-means가 거리기반으로 분류를 하기 때문이고 실제 데이터를 비교해 이유를 알 수 있다.

![count2](/assets/img/0922/count2.jpg)

파란색은 정확히 분리되어 거리기반으로 분류가 가능하나 나머지는 섞여 있어 거리만으로는 분류가 힘들다.

> 참고로 K-means로 분류한 결과 [0,1,2]가 실제 데이터 라벨은 아님을 알자!

## DBSCAN

kmeans는 거리기반이기 때문에 기하학적인 모양의 데이터를 분류하는데 매우 치명적인 약점을 가지고 있다.

DBSCAN은 밀도기반으로 clustering을 진행한다.
밀도기반이기 때문에 기하학적 모양의 데이터 분포도 잘 분류한다.

1. 몇개의 그룹으로 사용자가 나눌 필요가 없다 = K지정 필요 X

그렇다면 밀도가 낮은 부분은 -> -1로 나타난다.

![DBS](/assets/img/0922/DBSCAN.png)

학습, 예측방법은 아래와 같다.

```python
from sklearn.cluster import DBSCAN
'''
eps(입실론) : 반경(넓게하면...군집 수가 작아짐) / (좁게하면...군집 수가 많아진다.)
min_samples : 반경안에 몇개 이상의 데이터가 있을 때 하나의 그룹으로 인정할지 여부
'''
dbscan = DBSCAN(eps=0.7, min_samples=2)
# dbscan은 fit_predict으로 학습을 진행한다.
dbscan_data = dbscan.fit_predict(df.loc[:, 'sepal length (cm)': 'petal width (cm)'])
dbscan_data
```
![img](/assets/img/0922/%EA%B5%B0%EC%A7%91%ED%99%94%20%EA%B3%84%EC%82%B0.png)

a^i `응집도`, b^i `분리도`

    응집도가 높고 : 데이터끼리 촘촘
    분리도가 높은 : 그룸간의 거리가 먼