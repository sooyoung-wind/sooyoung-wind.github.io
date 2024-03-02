---
layout: post
title: 챗봇 개발을 위한 LLM 모델 선정 및 파라미터 추정   
tags: [NLP, LLM, chatbot]
categories: [Development]
sitemap:
  changefreq: daily
  priority : 1.0
---

챗봇 개발을 위해서 초기에 주어진 컴퓨터 자원을 고려해서 LLM 모델의 파라미터를 선정하고 이에 적합한 데이터 크기를 추정하고자 합니다.  

----

목차
- toc
{: toc }

----  

### 컴퓨터 자원 파악  

LLM 모델을 훈력하는데 가장 영향을 받는 부분은 GPU의 VRAM이라고 할 수 있습니다. 너무 작은 VRAM은 거대 언어 모델을 학습하는데 필요한 메모리가 상대적으로 적어서 학습 자체를 할 수 없기 때문입니다. 또한 모델을 로드할려면 모델의 파라미터의 수로 인해 필요한 메모리가 정해져 있습니다. 단, LLM 모델을 fine-tuning을 하지 않는다면 GPU에 대한 부담감은 다소 줄어든다고 볼 수 있습니다.   
컴퓨터에 설치된 GPU는 상세스펙을 확인하면 VRAM의 크기를 확인 할 수 있습니다. 

### GPU VRAM에 구동 가능한 LLM 모델 파라미터 범위 산출  

FS32(float32)을 기준으로 한다면 $32 bits / 8 bits = 4 bytes$ (1 byte는 8 bits 이므로)로 산출됩니다. 여기서, B(빌리온, $10^9$)과 GB(기가바이트, $10^9$)의 자릿수는 동일합니다.   

예를 들어 LLM 모델의 파라미터가 2B라고 가정한다면 FS32인 경우 아래와 같이 필요한 메모리를 추정할 수 있습니다.  
- LLM Load memory : 2B * 4 bytes = 8GB  
(모델 파라미터를 N개라고 할 경우 4N)  
- Optimizer(AdamW) : (2B * 4 bytes) * 4 = 32GB(16N)  
- Gradients : 2B * 4 bytes = 8GB(4N)  
- Total memory : 8GB + 32GB + 8GB = 48GB(24N)  

만약 inference만 필요한 경우에는 모델 메모리의 약 1.2배 정도 필요합니다. 자세한 내용은 참조 블로그를 읽어보시면 되겠습니다.  

[참조 블로그](https://blog.eleuther.ai/transformer-math/#total-inference-memory)  

### Test loss에 따른 최적값(계산투입량, 학습 데이터, 파라미터)  

원하는 Test loss가 있다면 아래 수식에 의해서 학습 데이터 및 파라미터의 크기를 추정할 수 있습니다. 반대로 학습 데이터와 모델의 파라미터가 정해졌다면 도달할 수 있는 loss 값을 사전에 미리 파악할 수 있습니다.  

Test Loss vs. Compute  
$$L = \left( \frac{C_{min}}{2.3 \cdot 10^8} \right)^{-0.050}$$  

Test Loss vs. Dataset Size   
$$L = \left( \frac{D}{5.4 \cdot 10^{13}} \right)^{-0.095}$$

Test Loss vs. Parameters  
$$L = \left( \frac{N}{8.8 \cdot 10^3} \right)^{-0.076}$$

![그림](/assets/img/my_photo/Post_20240303_1.png)  
[Kaplan et al, “Scaling Laws for Neural Language Models”, OpenAI, 2020](https://arxiv.org/pdf/2001.08361.pdf)  

아래 그림은 GPT3 모델를 위해서 최적의 파라미터를 추정하기 위한 실험 결과입니다.   
![그림](/assets/img/my_photo/Post_20240303_2.png)    
[Brown et al, “Language Models are Few-Shot Learners”, NeurIPS, 2020](https://arxiv.org/pdf/2005.14165.pdf)
