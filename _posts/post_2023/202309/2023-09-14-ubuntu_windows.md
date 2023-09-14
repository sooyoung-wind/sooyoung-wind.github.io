---
layout: post
title: 윈도우에서 ubuntu 환경을 사용하여 터미널을 꾸미기(zsh)
tags: [git, zsh, python]
categories: [git, zsh, python]
sitemap:
  changefreq: daily
  priority : 1.0
---
윈도우 환경에서 ubuntu 환경을 구축하여 터미널을 예쁘게 변경해보자.  

1. 윈도우 power shell을 관리자 권한으로 실행한다.   
2. 아래 명령어을 실행한다.  
  ```
  dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
  ```  

![명령어창](/assets/img/my_photo/git_0001.png)  

3. Microsoft Store(스토어 앱)을 실행해서 `ubuntu`를 검색한 후 다운로드를 한다.  

- LTS는 Long Term Support로 장기간 지원이되고 안정성이 우수한 버전을 뜻한다.  
- **다운로드가 완료되면 반드시 재부팅을 추천한다.**  

4. 재부팅 후, Ubuntu를 실행하면 "Installing, this may take a few minutes"라는 문구와 함께 설치가 시작된다.  

  > 4.1 "0x800701bc" 에러, 만약 아래 그림처럼 에러가 발생하면 아래 다운로드 링크를 통해서 WSL2 Linux Kernel Update를 설치해주면 해결된다.  


[다운로드 링크](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

![명령어창](/assets/img/my_photo/git_0002.png)  


5. UNIX username과 password를 입력하라고 한다. 초기설정이므로 본인이 원하는대로 설정한다.(나중에 사용해야 하니 기억해둘 것) 성공하면 아래와 같이 보인다.  

![그림3](/assets/img/my_photo/git_0003.png)  

6. `sudo apt install zsh` : zsh 설치하기  

7. 아래 명령어 실행해서 `oh my zsh`설치하기  

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

![그림3](/assets/img/my_photo/git_0004.png)  


8. `vi ~/.zshrc` vi 편집기로 .zshrc 파일 수정  
+ ZSH_THEME='agnoster'
+ 가장 많이 쓰이는 테마인거 같다.  


> 예쁘긴 한데 문제는 vscode에서 터미널을 불러와서 사용해 봤는데 커맨드를 명령하면 그 뒤에 나온는 반응이 좀 느리다. 불편할꺼 같아서 아마도 사용하지 않을꺼 같다. 다만, 추후에 사용할 수 도 있으니 글로 남겨둔다.  
