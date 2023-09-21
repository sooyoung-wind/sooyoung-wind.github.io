---
layout: post
title: Classification problem에 대한 노트
tags: [ML]
categories: [ML]
sitemap:
  changefreq: daily
  priority : 1.0
---

머신러닝(ML)에 입문과정이라고 볼 수 있는 Classification problem에 대해서 학습한 내용을 정리했다.  

----

목차
- toc
{: toc }

----  

### Loss Function 설명  

+ Gini index  
+ Entropy  
+ Missclassification Error (잘 사용하지 않음)  

Classification Loss Function  
+ 기본적으로 Classification Model을 학습할 때 사용하는 Binary Cross Entropy  
+ 로그함수에서 0과 1사이의 값을 이용하여 1에 가까울 수록 정답에 가깝다라는 의미를 부여한 loss function임  
+ 단, 정답이 거짓인 경우(false 또는 0)는 $$(1-y_i)$$와 같은 형태로 반대값이 나오도록 해서 1에 가까울 수록 정답에 가깝다라는 의미를 부여함  

### Descision Tree 원리  

교재 2장 12~16 페이지 참조 : Gini Index으로 사용하여 Split 하는 과정이 있음  

1. First Step은 불순도가 가장 낮은 Feature와 포인트를 찾음  
+ X1이 10이고 X2가 20이면 X1을 먼저 split을 진행한다.  
+ 쪼개는 방법은 min, max(gini index)를 구하고 중간 값을 기준으로 나눈다. 
+ 다 쪼갠 다음에 Information gain이 가장 큰 포인트를 찾아서 split를 최종적으로 결정한다. (gini index가 가장 낮은 값, Best position을 기준으로 분활된다.)  

> Decision Tree에서 Rule Extraction을 할 수 있다. 가장 중요하고 강력한 해석력을 가진다고 언급했다.  Simple하지만 직관력이 있고 결정트리의 깊이가 5 이상 깊어지면 Model이 복잡해져서 해석력은 현저히 떨어지게 된다.(분석자가 판단할 수 있어야 하니깐)


> 따라서 정확도가 떨어지더라도 Rule Extraction을 사용하여 해석을 시도할만한 가치가 있다고 본다.  

![Rlue Extraction](/assets/img/my_photo/ML_0007.png)  

### Model 평가 및 지표 해석  

Classification Model 평가 및 지표는 TP, FN, FP, TN 내용을 말한다.  
평가를 통해 class의 balance가 맞는지 확인해야 한다. 예를 들어서 총 1,100개의 데이터에서 1,000개의 데이터가 참이고 거짓이 100개이면 참의 경우가 너무 편중된 결과이다.  

+ 재현율 : 양성을 음성으로 잘못 판단하면 문제가 되는 경우(예 암진단)  
+ 정밀도 : 음성을 양성으로 잘못 판단하면 문제가 되는 경우(예 스팸메일)  

![평가지표 요약 2020](/assets/img/my_photo/ML_0008.png)  

F1에 대한 계산식이 변경되었으니 확인이 필요하다.  

![평가지표 요약 2023](/assets/img/my_photo/ML_0009.png)  
[Wiki 링크](https://en.wikipedia.org/wiki/Template:Diagnostic_testing_diagram)


### Ensemble 정의  

앙상블은 여러개의 모델을 조화롭게 학습시켜 그 모델들의 예측 결과들을 이용하여 더 정확한 예측 값을 구하는 방법을 말한다.  

> Ensembles almost always work BETTER !!!  


**[Note]**
MLConfSF(샌프란시스코 머신러닝 컨퍼런스, 2016) - Top 10 main takeawaays  
- It's (still) not all about Deep Learning (딥러닝이 전부가 아니다.)  
- Choose the right problem to solve, with the right mertric(상황에 맞는 평가 지표를 활용해라.)  
- Fine tuning your modles in 5% of a project(Model tuning 하는데 있ㄷ어서 시간을 많이 투자하지 마라.)  
- **"Ensembles almost aalways work better"**  
- The trend towards personalization(개인화 혹은 초개인화가 트랜드)  
- Manual curation of content is still used in practive(널리 알려진 컨텐츠들은 아직도 잘 먹힌다.) -> 2016년도 이후라서 실무적으로 잘 안먹힌다.  
- Avoid the curse of complexity(차원의 저주를 피해라, Overfitting)  
- Learn the best practives from established players(잘 알려진 혹은 유명한 BP 사례를 배워라.) -> 2016년도 이후라서 학습으로는 괜찮지만 결국 도메인에 맞쳐서 튜닝을 해야 함.  
- Everybody is using open source(오픈 소스를 잘 활용해라.)  
- Make sure you have support from the executives(프로젝트를 진행하기 전 경영진의 지원이 있는지 확인해라.)  


#### Ensenble(앙상블) 종류  
1. Bagging : Reduce the Variance  
    - 하나의 데이터 셋을 부트스탭(복원추출)을 통해 서로 다른 데이터 집단으로 생성해서 동일한 ML모델에 적용한다. 복원추출된 데이터셋은 전체 데이터셋에서 약 36.8%만큼 추출되지 않는 데이터가 존재한다.  
2. Stacking : Use another prediction model  
    - 동일한 하나의 데이터 셋을 기반으로 다양한 모델(SVM, Decision Tree, K-NN 등)을 적용하는 앙상블 방법을 말한다.  
3. Boosting : Reduce the Bais  
    - 예를 들어 Tree 결과에서 잘 분류되지 않는 데이터에 가중치를 부여해서 잘 분류될 수 있도록 다시 훈련한다. 이런 과정을 여러번 수행하여 새로운 분류 기준을 점차 확대하는 앙상블 방법을 말한다.  


### Random Forest  

랜덤 포레스트(Randowm Forest)는 decision tree를 배깅 기법으로 수많은 decision tree를 결합한 모델을 말한다.  
랜덤 포레스트는 배깅으로 인해 추출되지 않는 36.8% 데이터셋도 포함될 수 있도록 구현된다. 왜냐!!! 추가적으로 피처에 대한 랜덤추출도 사용하기 때문이다.(이건 배깅이 아니고 feature selection과 같은 결과, 예를 들면 10개의 피처에서 5개의 피처만 선택해서 데이터셋을 구성)  


> 랜덤 포레스트는 룰 추출(Rule Extraction)이 안되기 때문에(python은 없고, R은 있으나 대략적인거만 나옴), 랜덤 포레스트를 통해 관계성이 높은 상위 TOP5를 추출해서 결정트리에 입력하여 룰 추출을 수행한다.  

Out Of Bag == 36.8에 해당하는 데이터  

Feature Importance Score란?  
랜덤 포레스트에서 변수의 중요도를 판별할 수 있다. 먼저 원본 데이터셋에 대한 모델의 결과와 의도적으로 **변형된 데이터셋**에 대한 모델의 결과를 서로 비교한다.  
실질적으로는 데이터 결과값의 차이이며, 평균오차와 표준편차를 산출하여 평균/표준편차를 산출한다. 여기서 **평균/표준편차**가 Feature Importance Score이다. 각 피처별로 모델에 중요하다면 FI는 올라가게 되며 반대로 각 피처별로 모델에 중요하지 않다면 FI는 낮아지게 된다.  

실습 코드 : [랜덤 포레스트 링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap02.08.Random%20Forest%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)


### AdaBoost 

실습 코드 : [AdaBoost 코드 링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap02.10.Adaboost%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)  


### Gradient Boosting Machine(GBM)  

Classification 뿐만 아니라 Regression에서도 사용 가능  

+ 부스팅 가문의 족보  
    + Adaboost -> GBM -> XGB(2014) -> LGBM(2016) -> CATB(2017) -> NGB(2019)

GBM Main idea : Residual to Zero  

실습 코드 : [GBM 코드 링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap02.13.Gradient%20Boosting%20Machine%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)  


### XGBoost  

![계보](/assets/img/my_photo/ML_0010.png)  


실습 코드 : [XGBoost 코드 링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap02.16.XGBoost%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)  

### LightGBM  

실습 코드 : [LightGBM 코드 링크](https://github.com/sooyoung-wind/sooyoung-wind.github.io/blob/main/ipynb/ML_Basic_Course/Chap02.18.LightGBM%20Code%20%EC%8B%A4%EC%8A%B5.ipynb)  


