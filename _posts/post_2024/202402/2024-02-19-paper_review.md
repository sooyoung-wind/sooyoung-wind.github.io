---
layout: post
title: 논문 리뷰(DoRA, Weight-Decomposed Low-Rank Adaptation)  
tags: [NLP, LLM, PEFT]
categories: [NLP, LLM, PEFT]
sitemap:
  changefreq: daily
  priority : 1.0
---

논문 리뷰입니다. 논문 제목은 DoRA, Weight-Decomposed Low-Rank Adaptation입니다. LoRA에서 발전된 PEFT 계열 방법론으로 Direction vector와 Magnitude vector를 도입해서 성능을 개선한 연구입니다.  

---

논문 제목 : DoRA, Weight-Decomposed Low-Rank Adaptation  
저널 : ARXIV  
URL : [https://arxiv.org/abs/2402.09353](https://arxiv.org/abs/2402.09353)    
요약 : LoRA에서 발전된 PEFT 계열 방법론, Direction vector와 Magnitude vector를 도입해서 성능을 개선한 연구


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
DoRA는 LoRA의 구조를 포함한 형태로 이루어져 있다. 크게 Magnitude, Direction의 용어를 사용해서 2개의 학습 영역을 구분했다. Magnitude는 컬럼 방향($$R^d$$)을 1차원으로 축소하는 과정이다. decompose에 의해 벡터의 크기를 나눠주기 때문에 단위 벡터로 변환되고 이 단위 벡터는 $$m$$ 벡터에 의해 다시 scaling up된다. 즉, 각 $$R_d$$ 방향에 해당하는 벡터들을 기준으로 가중치의 방향성만 유지한 다음 $$m$$ 벡터의 스칼라 만큼 벡터의 크기를 재정렬하는 개념이라고 볼 수 있다.
Direction 벡터의 내부는 LoRA 구조를 따르고 있어서 동일한 구조로 구성되었다.

$$W = m {V \over \lvert \lvert V \rvert \rvert_c}$$

![그림](/assets/img/my_photo/Post_20240219_1.png)  

### Weight Decomposition Analysis  

$$\triangle M$$은 규모(magnitude vector)에 대한 사전 훈련 모델의 weight 값과 fine-tuning weight 값의 차이를 나타냅니다. $$\triangle D$$ 는 방향(direction vector)에 대한 pre-trained weight $$W_0$$ 과 fine-turning weight $$W_{FT}$$  값 차이를 의미합니다. 이러한 방법으로 LoRA와 DoRA도 계산하여 아래 그림과 같이 분석했습니다.  
$$\triangle M$$은 MAE와 같은 개념으로 값이 0에 가까울 수록 차이가 없고 $$\triangle D$$은 상관계수와 같은 개념으로 cos 유사도(실제로는 $$1-cos$$ 으로 계산)가 계산되고 있어서 0에 가까울 수록 유사성이 높습니다.  

$$\Delta M_{FT}^{t} = \frac{1}{k} \sum_{n=1}^{k} \left | m_{FT}^{n,t} - m_{0}^{n} \right |$$  

$$\Delta D_{FT}^{t} = \frac{1}{k} \sum_{n=1}^{k} \left( 1 - \cos(\mathbf{W}_{FT}^{n,t}, \mathbf{W}_{0}^{n}) \right)$$  

![그림](/assets/img/my_photo/Post_20240219_2.png)  

FT결과와 LoRA결과를 살펴보면, 기울기가 서로 상반되는 패턴을 볼 수 있다. FT는 $$\triangle M$$이 낮아지면 $$\triangle D$$가 높아지는 음의 방향 기울기를 보이는 반면 LoRA의 결과는 동시에 증가하는 양의 방향 기울기를 보여주고 있습니다.  
> 저자들은 이런 패턴을 보면서 LoRA는 세심한 조정이 FT에 비해 힘들다고 판단하고 있으며 FT의 결과를 기준으로 FT의 패턴과 유사해야 함을 강조했습니다. 따라서 DoRA가 FT와 유사한 음의 방향인 기울기를 보여주고 있으므로 **본 연구에서는 FT와 더 유사한 학습 패턴을 나타내며 LoRA보다 학습 능력을 향상시킬 수 있는 LoRA의 변형을 제안하는 것을 목표한다고 언급했습니다.**  
상관계수는 FT가 -0.62, DoRA가 -0.31, LoRA가 0.83이라고 언급했습니다.  


### Gradient of Loss  
아래 식은 DoRA에서 사용되는 Gradient of Loss이다. (a)와 (b)식은 $$\mathbf{W}' = m \frac{\mathbf{V} + \Delta \mathbf{V}}{\| \mathbf{V} + \Delta \mathbf{V} \|_{c}}$$에서 도출되었다.  $$\mathbf{V} + \Delta \mathbf{V}$$을 $$\mathbf{V}'$$로 치환해서 전개했다고 한다.  

$$\nabla_{\mathbf{v}} L = \frac{m}{\|\mathbf{V}\|_{c}} \left( \mathbf{I} - \frac{\mathbf{V}\mathbf{V}^{T}}{\|\mathbf{V}\|_{c}^2} \right) \nabla_{\mathbf{w}} L$$  (a)

$$\nabla_{m} L = \frac{\nabla_{\mathbf{w}} L \cdot \mathbf{V}'}{\| \mathbf{V}' \|_{c}}
$$      (b)

여기서 (Salimans & Kingma, 2016)의 최적화 장점을 위해서 weight normalization을 도입했다고 한다. 참조 식은 ref 1부터 ref 3까지의 식이고 코드와 논문은 링크를 참조하시면 됩니다.  

$$\mathbf{w} = \frac{g}{\|\mathbf{v}\|} \mathbf{v}$$      (ref 1)  

$$\nabla_{g_{\perp}} L = \frac{\nabla_{\mathbf{w}} L \cdot \mathbf{v}}{\|\mathbf{v}\|}, \quad \nabla_{\mathbf{v}_{\perp}} L = \frac{g}{\|\mathbf{v}\|} \nabla_{\mathbf{w}} L - \frac{g \nabla_{g_{\perp}} L}{\|\mathbf{v}\|^2} \mathbf{v}$$  (ref 2)  

$$\nabla_{\mathbf{v}_{\perp}} L = \frac{g}{\|\mathbf{v}\|} \mathbf{M}_{\mathbf{w}} \nabla_{\mathbf{w}} L, \text{ with } \mathbf{M}_{\mathbf{w}} = \mathbf{I} - \frac{\mathbf{w}\mathbf{w}'}{\|\mathbf{w}\|^2}$$    (ref 3)  

[Weightnorm paper link](https://proceedings.neurips.cc/paper/2016/file/ed265bc903a5a097f61d3ec064d96d2e-Paper.pdf)  
[GitHub Code Link](https://github.com/openai/weightnorm)  

> **[Notice]**  
> 여기서 중요한 포인트는 논문 4.3 Reduction of Training Overhead에서 언급한 대로 training memory를 줄이기 위해서 $$\mathbf{W}' = m \frac{\mathbf{V} + \Delta \mathbf{V}}{\| \mathbf{V} + \Delta \mathbf{V} \|_{c}}$$의 분모에 있는 $$\| \mathbf{V} + \Delta \mathbf(V)\|_c$$을 상수로 취급했다. 이를 통해 LLaMA의 fine-tuning 케이스에서 메모리가 24.4%p 감소했고 성능도 비슷한 수준을 보여줬다. (코드로 구현할 경우 중요한 포인트!)  

### Experiments  
아래 그림은 학습 샘플 수에 따른 LLaMA2-7B의 Score 결과를 보여주고 있다. DoRA를 적용한 경우가 낮은 샘플 수에서도 안정적인 Score를 보여주었다.  

![그림](/assets/img/my_photo/Post_20240219_3.png)  

논문에서 Rank r에 따라 정확도를 평가했는데 LoRA는 Rank에 의한 정확도 편차가 존재했는데 DoRA는 Rank에 의한 정확도 변동성이 적음을 확인할 수 있었다.  

![그림](/assets/img/my_photo/Post_20240219_4.png)  

> 모델 강건성(Robustness)에 대해서 DoRA가 LoRA보다 좋다고 주장하고 있는 논문이였다. 추가적으로 논문 저자는 directional update가 magnitude update보다 훈련해야할 파라미터가 많으므로 동시에 direction 과 magnitude를 학습하는 것 보다는 magnitude만을 학습해서 개선의 여지가 있는지 확인할 필요성이 있다고 언급하였다.  


