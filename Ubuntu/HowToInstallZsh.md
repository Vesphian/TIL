# 우분투에 zsh 설치하고 테마 적용하기

 1. 먼저 zsh 설치 유무 확인하기 

    ```
    $ zsh --version
    zsh 5.0.7
    ```
    
    
2. 설치되어 있지 않다면 설치하기

    ```
    sudo apt install zsh
    ```
    
    
 3. bash 쉘을 zsh로 변경

    ```
    $ which zsh    // zsh의 위치를 확인
    /usr/bin/zsh
    
    $ chsh -s /usr/bin/zsh   // 기본 쉘을 변경
    
    $ chsh -s 'which zsh'   // 두 명령어를 한 줄로!
    ```
    
    
 4. 터미널 재실행 후 쉘 확인
    ```
    echo $SHELL
    /usr/bin/zsh
    ```
    
    
 5. oh my zsh 설치  
    http://ohmyz.sh/ 
    ```
    sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
    
    sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"   // wget으로도 가능
    ```
    
    curl 이 없다면 설치.  
    `sudo apt install curl`
    
    
 6. zsh 테마 변경  
    [다양한 테마와 예시](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)  
    agnoster 테마 (https://github.com/agnoster/agnoster-zsh-theme)  
    ![agnoster](https://cloud.githubusercontent.com/assets/2618447/6316862/70f58fb6-ba03-11e4-82c9-c083bf9a6574.png)
    
    사용자 홈 디렉토리에 .zshrc 를 수정 해야 한다.  
    `sudo vim ~/.zshrc`  
    여기서   
    `THEME="rubbyrussell"` 부분을 `THEME="agnoster"` 로 변경한다.
    
    기존 부분을 주석 처리 하고 새로 써도 된다.      
    `source ~/.zshrc` 를 입력 해서 바로 적용 시켜준다.
    
    ~~여기서 ~/.zshrc 의 rc는 Run commands의 준말이다.~~
    
    
 7. 높은 확률로 깨진 폰트를 되살려보자.  
    https://github.com/powerline/fonts      
    여기로 가서 Clone or Download를 눌러서 나오는 https://github.com/powerline/fonts.git 이 주소 디렉터리를 복사합니다.
    
    ```git clone https://github.com/powerline/fonts.git```
    
    역시 여기서 git이 없으면 설치해 줍니다.      
    `sudo apt install git`
    
    디렉터리 복사 후 폰트를 설치 합니다.
    ```
    cd fonts
    ./install.sh
    ```
    
    이제 터미널 설정 **편집 > 프로파일 기본 설정**으로 가서 일반의 **사용자 지정 글꼴**을 체크하고      
    글꼴을 `Ubuntu Mono derivative Powerline Regular`로 변경합니다.  
    저는 14포인트를 사용 합니다.
    
    
 8. 완성!  + 추가 정보
 
    dot(.)파일 링크 (https://dotfiles.github.io)
    
    `/home/encodes/Projects`라는 폴더가 있을 때 `cd /h/enco/proj`만 입력하고 탭을 치면 전체 경로가 완성된다.
    
    ```
    $ cd /h/enco/proj             # 여기까지 입력한 후 탭을 친다.

    $ cd /home/encodes/Projects   # 이렇게 결과가 나온다.
    ```
    
    루트`/` 디렉토리에서 바로 이전의 디렉토리인 `/home/encodes/Projects`로 이동하려면 간단히 `cd -`를 입력하면 된다.  
    Oh My Zsh에서는 이것을 확장하여 `cd -` 상태에서 탭을 치면 이전 히스토리가 나온다.  
    (최대 10개까지 보여주며, 2개 이상일 때 유용하므로 cd 명령으로 여러 개의 디렉토리를 이동한 후에 사용해 보자)  
    히스토리 바로 앞의 숫자를 입력하면 원하는 디렉토리로 돌아갈 수 있다.
    
    명령어 스펠체크기능은 디폴트가 아니기 때문에 다음 명령어로 깨워야 한다.  
    ```
    $ setopt correct                        # 스펠체크기능을 깨운다.
    $ gut add                               # 잘못된 명령어를 입력하면
    zsh: correct 'gut' to 'git' [nyae]?     # 예상되는 추천 명령어를 제시한다.
    ```
    
    또 oh-my-zsh에는 다양한 플러그인이 있는데 [Plugins Overview](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins-Overview)와 [Plugins](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)에서 더 살펴볼 수 있다.
    
    zsh가 엄청나게 느리게 시작할때: `sudo rm -rf /private/var/log/asl*.asl`
    
    작성 참고 : https://nolboo.kim/blog/2015/08/21/oh-my-zsh/, http://programmingskills.net/archives/115, https://the-illusionist.me/47
