---
layout: post
title: ML 알고리즘 분석을 위한 Clustering   
tags: [ML]
categories: [ML]
sitemap:
  changefreq: daily
  priority : 1.0
---

머신러닝(ML)에 입문과정이라고 볼 수 있는 Clustering에 대해서 학습한 내용을 정리했다.  

----

목차
- toc
{: toc }

----  

### Distance 개념  

1. 유클리디안 거리 (Euclidian Distance)  
2. 맨하탄 거리(Manhattan Distance)  
3. 코사인 거리(Cosine Distance)  
4. 민코프스키 거리(Minkowski Distance)  
        - m이 1이면 맨하탄 거리  
        - m이 2이면 유클리디안 거리  
5. 체비셰프 거리(Chebychev Distance)  
        - 인접한 8개의 모든 셀들을 같은 거리로 처리하는 방법  
6. 리벤슈테인 거리(Levenshtein Distance)  
        - 두 개의 문자열 A, B가 주어질 때 유사도를 계산하는 방법    
7. 마할라노비스 거리(Mahalanobis Distance)  
        - 데이터의 분포를 고려  
        - 데이터 군집에 가까우면 가깝다고 계산하는 방법


### K-mean 

Clustring은 비지도학습에 속하며 K-means 알고리즘은 데이터를 K개의 군집으로 분류해주는 알고리즘이다.  

Step :  
1. 군집의 개수(K) 설정  
2. 초기 중심점 설정  
3. 데이터를 군집에 할당(배정)  
4. 중심점 재설정(갱신)  
5. 데이터를 군집에 재할당(배정)  

### Hierarchical Clustering  

K-means와 달리 군집 수(K)를 사전에 정하지 않아도 학습을 수행할 수 있음 (개제들이 결합되는 순서를 나타내는 트리 형태의 구조인 덴드로그램(Dendrogram))  
덴드로그램 생성한 후 적절한 수준에서 트리를 자르면 전체 데이터를 몇 개 군집으로 나눌 수 있게 됨  

Step :  
1. HC를 수행할려면 모든 개체들 간 거리(distance)나 유사도(similarity)가 이미 계산되어 있어야 함  
2. 거리가 가까운 관측치들끼리 차례대로 군집으로 묶음  
        + Min distance  
        + Max distance  
        + Group average distance  
        + Between centroids distance  
3. 군집과 데이터(군집)간 거리를 다시 계산함  
4. 분석 대상 관측치가 하나도 없으면 학습을 종료  

<br>  

- K-means와 달리 사전에 군집수 K를 설정할 필요없음  
- 덴드로그램의 최상층을 끊어주면 두 개의 군집이 도출 됨  
_ 두번째 층을 끊으면 3 개의 군집이 도출 됨  
- HC의 경우 계산 복잡성은 $$O(n^3)$$으로 K-means보다 무거운 편임  

### Spectral Clustering  

Spectral Clustering을 수행하기 위해서는 데이터를 그래프로 변환하기 위해 인접행렬(Adjacency Matrix)을 만들어야 함  
                - 인접행렬을 만들 때 보통 가우시안 커널을 많이 사용함  
무방향 가중치 그래프(Undirected Weighted Graph)를 사용함  

### DBSCAN  

DBSCAN : Density-Based Spatial Clustering of Applications with Noise  

- K-mean나 Hierarchical Clustering의 경우 군집 간의 거리를 이용한 Clustering 기법  
- DBSCAN은 밀도 기반의 기법이며 세밀하게 몰려 있어서 밀도가 높은 부분은 Clustering 하는 기법  
따라서, `e`와 `m`이 Hyperparameter이다.  
- Core point : `e`와 `m`이 충족된 데이터  
- Boders : `e`와 `m`이 불충족된 데이터이지만, core point로 하는 군집에 소속된 데이터 즉, boder point(경계점)이라고 함  
- Noise : `e`와 `m`이 불충족된 데이터이고 boder point도 아닌 경우  

**DBSCAN**  
[장점]  
- K-means와 달리 Cluster의 수를 정하지 않아도 됨  
- Cluster의 밀도에 따라 Cluster를 서로 연결하기 때문에 기하학적인 모양을 갖는 군집도를 잘 찾을 수 있음  
- DBSCAN을 활용하여 이상치를 발견할 수 있음  
- DBSCAN은 Cluster결과가 이상치에 영향을 받지 않음  
- 다양한 모양의 Cluster 패턴도 잘 잡아 낼 수 있음  
- 구현이 비교적 쉬움  

[단점]  
- 고차원 데이터에 대해서 잘 작동하지 않음  
- Sparse Data에 대해 결과가 좋지 못함  


### HDBSCAN  

HBDSCAN : Hierarchical Density-Based Spatial Clustering of Apoplications with Noise  

DBSCADN은 Local density에 대한 정보를 반영해 줄 수 없으며 Data들의 계층적 구조를 반영한 Clustering이 불가능함  
HDBSCAN의 경우 더 이상 epsilon(e)이 필요하지 않음  
+ Transform the space according to the density/sparsity  
                - Distance를 더 Robust하게 만들어주는 작업  
                - $$core_k(a)$$: a의 이웃과의 거리  
                - $$core_k(b)$$: b의 이웃과의 거리  
                - $$d(a, b)$$: a, b의 자체 거리  
                - dense한 지점의 data는 $$core_k$$가 매우 작기 때문에 $$d(a,b)$$값을 사용하고, dense가 낮은 경우 $$core_k$$의 주변 정보를 사용하게 됨  
                - Distance의 robustness를 늘리고, 최종적으로는 더 효율적인 Clustering을 가능하게 함  
+ Build the minimum spanning tree of the sistance weighted graph  
                - Robust Distance를 계산하고 정보를 가지고 Minimum spanning tree를 구축함  
                - Spanning Tree를 모든 정점들이 연결되어 있어야 하고 사이클을 포함해서는 안됨  
+ Construct a cluster hierarchy of connected components  
                - 계층(Hierarchy)를 만드는 과정 중 하나  
                - Robust Distance의 Cut을 점점 높여가며 하나씩 Graph의 Edge를 끊어 나감  
                - 그리고 만들어진 Minimum spanning tree를 가장 가까운 거리부터 묶어줌  
                - HC에서 군집들 간 거리를 계산해서 묶어 주는 것과 같은 원리  
+ Condense the cluster hierarchy based on minimum cluster size  
                - Robust Distance가 0.4 이하로는 거의 모든 데이터가 1개로 떨어져 나오는 지저분한 상황이 발생함(이러한 경우 Noise처럼 보일 수 있음)  
                - 따라서 minimum size 이상의 크기를 가진 component들만 남게 만듬  
                - minimum size는 HDBSCAN의 Hyperparameter 이다.  
+ Extract the stable clusters from the condensed tree  


DBSCAN vs. HDBSCAN  
- DNSCAN은 Dense를 제대로 못 잡아 내고 주변에 있는 데이터를 전부 Noise 처리를 함  
- epsilon의 문제가 발생  


### Clustering 평가 지표  

Clustering 결과에 대한 평가지표는 확실한 지표가 없다. Clustering은 비지도 학습이기 때문에 정답과 비교할 수 없기 때문이다.  

Clustering 평가 지표  
- External : 우리가 알고 있는 정답(Label)과 비교해 봄(있을 수 없음, 그래서 비지도 학습임)  
- Internal : Cluster들의 공간들을 확인해보는 방법  
- Relative : Cluster의 공간과 Cluster들 간 떨어진 정도를 가지고 판단할 수 있음  

Dun Index  
- 만약 clustering이 잘 작동 했다면 군집간의 거리는 커야 하며, 군집내의 대표 값 및 데이터 간의 거리는 작아야 한다.  

Silhouette Score  
- Dunn Index의 경우 Clustering의 유효성을 검증하기 위한 하나의 값이 있음  
- Silhouette Score는 개체별로 그 적합성이 평가 됨 (값이 1에 가까울수록 군집화가 잘 되었다고 판단함)  

Clustering 종류  

추천 링크 : https://scikit-learn.org/stable/modules/clustering.html

