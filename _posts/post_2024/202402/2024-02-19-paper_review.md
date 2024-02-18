---
layout: post
title: 논문 리뷰(DoRA, Weight-Decomposed Low-Rank Adaptation)  
tags: [NLP, LLM, PEFT]
categories: [NLP, LLM, PEFT]
sitemap:
  changefreq: daily
  priority : 1.0
---

논문 제목 : DoRA, Weight-Decomposed Low-Rank Adaptation  
저널 : ARXIV  
URL : https://arxiv.org/abs/2402.09353  
요약  
LoRA에서 발전된 PEFT 계열 방법론, Direction vector와 Magnitude vector를 도입해서 성능을 개선한 연구


----

목차
- toc
{: toc }

----  

### Parameter-Efficient Fine-Tuning(PEFT)

PEFT(Parameter-efficient fine-tuning)은 추가적인 추론을 필요하지 않으면서 사전 확습된 모델의 성능을 개선하기 위해 머신 러닝 분야에서 많이 쓰이고 있는 방법입니다. 본 연구에서는 PEFT를 3가지 카테고리로 나누고 있습니다.

1. Adapter-based methods  
- 기존 backbone은 frozen하고 추가적으로 훈련이 가능한 module을 추가하는 방법  
- Houlsby et al. 2019은 linear module을 출력 layer에 결합해서 성능을 개선  
2. Prompt-based methods  
- 외부 soft tokens(prompts)을 추가하여 훈련 가능한 벡터만 집중하는 방법  
- soft toknes에 부여된 초기 값에 의해 결과가 상당히 달라지는 문제 있음  
3. LoRA methods  
- 추가적인 추론 계산을 증가시키지 않기 위해서 Low-Rank 매트릭스를 도입한 방법  
- SVD(Singular Value Decomposition)을 활용한 LoRA 등 다양하게 파생된 LoRA가 있음[(SVD 관련 블로그)](https://darkpgmr.tistory.com/106)  

### DoRA 구조    
DoRA는 LoRA의 구조를 포함한 형태로 이루어져 있다. 크게 Magnitude, Direction의 용어를 사용해서 2개의 학습 영역을 구분했다. Magnitude는 컬럼 방향($R^d$)을 1차원으로 축소하는 과정이다. decompose에 의해 벡터의 크기를 나눠주기 때문에 단위 벡터로 변환되고 이 단위 벡터는 $m$ 벡터에 의해 다시 scaling up된다. 즉, 각 $R_d$ 방향에 해당하는 벡터들을 기준으로 가중치의 방향성만 유지한 다음 $m$ 벡터의 스칼라 만큼 벡터의 크기를 재정렬하는 개념이라고 볼 수 있다.
Direction 벡터의 내부는 LoRA 구조를 따르고 있어서 동일한 구조로 구성되었다.

$$W = m {V \over \lvert \lvert V \rvert \rvert_c}$$

![그림](/assets/img/my_photo/Post_20240219_1.png)  

### Weight Decomposition Analysis  

$\triangle M$은 규모(magnitude vector)에 대한 사전 훈련 모델의 weight 값과 fine-tuning weight 값의 차이를 나타냅니다. $\triangle D$ 는 방향(direction vector)에 대한 pre-trained weight $W_0$ 과 fine-turning weight $W_{FT}$  값 차이를 의미합니다. 이러한 방법으로 LoRA와 DoRA도 계산하여 아래 그림과 같이 분석했습니다.  
$\triangle M$은 MAE와 같은 개념으로 값이 0에 가까울 수록 차이가 없고 $\triangle D$은 상관계수와 같은 개념으로 cos 유사도(실제로는 $1-cos$ 으로 계산)가 계산되고 있어서 0에 가까울 수록 유사성이 높습니다.  

$$\Delta M_{FT}^{t} = \frac{1}{k} \sum_{n=1}^{k} \left | m_{FT}^{n,t} - m_{0}^{n} \right |$$  

$$\Delta D_{FT}^{t} = \frac{1}{k} \sum_{n=1}^{k} \left( 1 - \cos(\mathbf{W}_{FT}^{n,t}, \mathbf{W}_{0}^{n}) \right)
$$  

![그림](/assets/img/my_photo/Post_20240219_2.png)  

FT결과와 LoRA결과를 살펴보면, 기울기가 서로 상반되는 패턴을 볼 수 있다. FT는 $\triangle M$이 낮아지면 $\triangle D$가 높아지는 음의 방향 기울기를 보이는 반면 LoRA의 결과는 동시에 증가하는 양의 방향 기울기를 보여주고 있습니다.  
저자들은 이런 패턴을 보면서 LoRA는 세심한 조정이 FT에 비해 힘들다고 판단하고 있으며 FT의 결과를 기준으로 FT의 패턴과 유사해야 함을 강조했습니다. 따라서 DoRA가 FT와 유사한 음의 방향인 기울기를 보여주고 있으므로 **본 연구에서는 FT와 더 유사한 학습 패턴을 나타내며 LoRA보다 학습 능력을 향상시킬 수 있는 LoRA의 변형을 제안하는 것을 목표한다고 언급했습니다.**  
상관계수는 FT가 -0.62, DoRA가 -0.31, LoRA가 0.83이라고 언급했습니다.  


### Gradient of Loss  
아래 식은 DoRA에서 사용되는 Gradient of Loss이다. 

$$\mathbf{w} = \frac{g}{\|\mathbf{v}\|} \mathbf{v}$$  
$$\nabla_{g_{\perp}} L = \frac{\nabla_{\mathbf{w}} L \cdot \mathbf{v}}{\|\mathbf{v}\|}, \quad \nabla_{\mathbf{v}_{\perp}} L = \frac{g}{\|\mathbf{v}\|} \nabla_{\mathbf{w}} L - \frac{g \nabla_{g_{\perp}} L}{\|\mathbf{v}\|^2} \mathbf{v}
$$  
$$\nabla_{\mathbf{v}_{\perp}} L = \frac{g}{\|\mathbf{v}\|} \mathbf{M}_{\mathbf{w}} \nabla_{\mathbf{w}} L, \text{ with } \mathbf{M}_{\mathbf{w}} = \mathbf{I} - \frac{\mathbf{w}\mathbf{w}'}{\|\mathbf{w}\|^2},
$$  

[Weightnorm paper link](https://proceedings.neurips.cc/paper/2016/file/ed265bc903a5a097f61d3ec064d96d2e-Paper.pdf)  
[GitHub Code Link](https://github.com/openai/weightnorm)  

