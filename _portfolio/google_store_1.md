---
layout: post
title: Google Store
img: "assets/img/portfolio/google_store.png"
date: 26-07-2023
tags: [Portfolio]
---

![image]({{ page.img | relative_url }})

[구글스토어 앱 링크](https://play.google.com/store/apps/details?id=com.windcare.jejuforecast&hl=ko-KR)

구글스토어에 처음으로 등록한 앱이다. [Apache cordova](https://cordova.apache.org/)를 사용해서 만들었다. 개발기간은 6개월이 걸렸다. 네이트브 언어를 사용하지 않고 cordova를 선택한 이유는 단지 빠른 시간에 앱을 등록하고 싶은 마음에 시작했다. 당시에 html의 개념을 어느정도 이해하고 있어서 새로운 툴을 배우는 것보다 빠르게 시작할 수 있는 방법이라고 생각했다. 그래서 6개월이라는 짧은 시간에 디자인부터 시작하여 앱출시까지 6개월이라는 짧은 시간이 걸렸다. 다른 사람이 보기엔 긴 시간이라고 볼 수 있지만 나는 앱을 출시하는 첫걸음치고 만족하고 있다.  

나는 html, css, js를 잘 알지 못한다. 다만 cordova의 특성상 간단한 코드만 가지고 구현이 바로 되어서 기초적인거부터 시작했다. 메인 페이지를 만들어 버튼을 클릭하면 원하는 페이지로 이동하는 아주 간단한 과정을 구현했다. 그 다음에는 각 페이지에 필요한 데이터를 불러오기 위해서 jQuery를 사용했다. 내가 운영하는 기상모델 서버에서 생성된 데이터를 보여주기 위해서다. 따로 DB를 만들지고 않아서 file db로 불러오기만 하면 되었다. 처음에는 로드가 안되어서 코드상에 문제가 있는줄 알았다. 몇일 고생 끝에 SSL 문제 때문이라는걸 확인하였고 이를 해결하니 다행이도 잘 동작하였다. 역시 옆에서 알려주는 사람이 없이 혼자서 만든는건 힘든일이라는걸 느낀다.  

완벽한 앱은 아니라는걸 앱출시한 후 몇일 사용해보면서 많이 느끼게 된다. 최근에는 flutter라는 세상을 알게 되어 이제는 flutter를 공부하기 시작했다. 