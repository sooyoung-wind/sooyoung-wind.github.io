---
layout: post
title: Regression problem에 대한 노트
tags: [ML]
categories: [ML]
sitemap:
  changefreq: daily
  priority : 1.0
---

머신러닝(ML)에 입문과정이라고 볼 수 있는 Regression problem에 대해서 학습한 내용을 정리했다.  

----

목차
- toc
{: toc }

----  

### $${\beta}$$(계수) 검증(p-value)

Simgple 또는 Multi-Linear Regression에서 추정한 $$\beta$$에 대한 검증을 귀무가설과 대립가설을 이용하여 수행한다.  

$${\beta}$$
에 대한 p-value가 낮으면 기울기가 0이 아닐 가능성이 높다고 판단  
- 통상적으로 p-value가 0.05 이하면 의미 있다고 판단 (베타의 기울기아 0일 확률이 0.05 이하라고 해석함)
- 즉, p-value가 0.05 이하면 $$H_0$$(귀무가설)은 기각되며 $$H_1$$이 채택됨  

![귀무가설대립가설](/assets/img/my_photo/ML_0001.png)  

> 이걸 일일히 검증하는 절차를 수행하다록 노력해보자.  

### Model Output 해석 방법  

- Factor 1: $$\beta_i$$($$\beta_0$$ 제외)  
- Factor 2: p-value  

$${\beta}$$ 가 크더라도 p-value가 높으면 의미가 없는 독립변수로 생각해야 한다. 따라서 위에서 Factor 1과 Factor2의 조건이 동시에 만족하는 독립변수만 유의미한 변수라고 판단할 수 있다.  

### Model 평가 및 지표 해석  

Model의 평가는 동일한 평가 기준으로 Model의 성능을 평가해야 함  

결정계수 $$R^2$$은 아래 그림 참조  
- SSR : Residual Sum of Squares (회귀식에서 설명되지 않는 편차)  
- SSE : Explained Sum of Squares (회귀식에서 설명되는 편차)  
- SST : Total Sum of Squares   

$$R^2 = {SSE \over SST} = 1 - {SSR \over SST}$$ 

![SSR_SSE_SST](/assets/img/my_photo/ML_0002.png)  

- 평균오차(ME)  
- 평균절대오차(MAE)  
- 평균절대비율오차(MAPE)  
- MSE  
- RMSE  

> **[Note]**  
> - Model 성능 Check - 정성, 정량  
> - P-value를 확인하여 의미 있는 변수 추출  
> - $$\beta$$ 활용, X 1단위 증가 당 Y에 얼마 만큼 영향을 미치는지 판단  

### Feature Selection  

Bias와 Variance의 Trade-off 최적점을 도출 해야 함  

$$Error(X) = Noise(X) + Bias(X) + Variance(X)$$

너무 많은 독립변수는 모델의 복잡도(complexity)를 높아지기 때문에 적절하게 선택할 필요성이 있다.  

1. 완전탐색(Exhaustive Search) : feature의 모든 조합의 수만큼 모델을 탐색하는 방법이다. 많은 시간이 소요되므로 비추천  
2. Forward Selection : feature를 하나씩 추가하면서 평가 지표가 높은 feature를 선정해가는 방법  
3. Backward Elimination : feature를 처음부터 다 넣고 하나씩 제외하면서 평가 지표가 높은 Feature를 선정해가는 방법  
4. Stepwise Selection : Forward와 Backword 방법을 번갈아 가면서 적용하면서 Feature들을 선정하는 방법   

### Penalty Term  

앞에서 배운 전통적인 Feature Selection 방법은 variables가 커짐에 따라 시간이 매우 오래 걸리고 최적의 variables subset을 찾기 어렵다. 즉, 가성비가 떨어지기 때문에 현재는 잘 쓰이지 않는다.  

Embedded Mehtod : Regularization Approach를 활용하여 Model이 스스로 Feature Selection을 진행하는 방법  
Model에서 불필요한 Feature에게 **벌점**을 부여해서 학습하지 못하게 한다.  

![penalty](/assets/img/my_photo/ML_0003.png)  

### Regularized Model-Ridge  

+ Ridge regression : $$\beta^2$$에 penalty term을 부여하는 방식  
+ Penalty Term을 추가한 Regularized Model의 경우 **Feature간 Scaling이 필수**  

$$\lambda$$ : 하이퍼 파라미터  
$$\beta^2$$에 penalty term을 부여하는 방식을 $$L_2-norm = L_2$$ Regulariation이라고 한다.  

![penalty](/assets/img/my_photo/ML_0004.png)  

+ Ridge regression 특징  
    - Ridge는 해 공간에서도 볼 수 있듯 Feature Selection은 되지 않음  
    - 불필요한 Feature는 충분히 0에 거의 수렴하게 만들어 버림  
    - Ridge Regression은 Feature의 크기가 결과에 큰 영향을 미치기 때문에 Scaling이 중요함  
    - 다중공선성(Multicollinearity) 방지에 가장 많이 쓰임  

실습 코드 : [링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap01.07.Regularized%20Model-Ridge%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)

### Regularized Model - LASSO  

LASSO는 $$L_1-norm$$ Regularization에 Penalty Term을 부여하는 방식  

Numberical Optimization Method 필요  
- Quadratic Programming techniques  
- LARS Algorithm  
- Coordinate descent Algorithm  
- Semi-differentiable  

Gradient Descent : 경사 하강법   
- Local Minima에 빠질 수 있음  
- Global Minimum이 있는지 알 수 없음  

![최적화 모델 종류](/assets/img/my_photo/ML_0005.png)   

![Ridge와 Lasso의 장단점](/assets/img/my_photo/ML_0006.png)  

실습 코드 : [링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap01.09.Regularized%20Model-LASSO%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)

> **[Data Split]**
> Data를 Split할 때는 BigData의 경우 꼭 indexing을 추출하여 모델에 적용해야 메모리 관리에 좋다.  

진행순서  
0. 데이터 파악  
1. X, Y 데이터로 분류  
2. DATA scaling  
3. Baseline 모델 수행  
4. 모델 최적화  
4.1 CV(Cross Validation) 사용하여 Best alpha 값 찾기 (alpha값이 Penalty를 의미함)  
4.2 for문으로 직접 panalty 값을 조정하면서 alpha 값 찾기  
5. Best alpha 값을 사용하여 모델 수행  

### Regularized Model - ElasticNet  

ElasticNet은 $$L_1-norm$$과 $$L_2-norm$$를 합친 모델이다. 단점은 Rigde, LASSO보다 더 많은 실험(파라미터 조정)이 필요하다.  

그외 Regularization method  
+ Elastic Net : 상관관계 높은 변수들 동시에 선택  
+ Fused Lasso : 인접한 변수들 동시에 선택  
+ Group Lasso : 사용자가 정의한 그룹 단위로 변수 선택  
+ Grace : 사용자가 정의한 그래프의 연결 관계에 따라 변수 선택  


