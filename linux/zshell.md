## zsh 설치
brew install zsh

## oh-my-zsh 설치
oh-my-zsh은  zsh을 좀 더 편리하게 이용하게 이용해주는 일종의 zsh 플러그인입니다. oh-my-zsh은 아래 명령어를 통해 설치할 수 있습니다. 터미널에 아래 명령어를 입력해주세요.

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```


## Agnoster 테마 설치하기
```
$ vi .zshrc
```

```
ZSH_THEME="agnoster" 수정
```

### 폰트 설정
이설정을 안하면 깨지는 현상 발생

[폰트 설치](https://github.com/powerline/fonts)

아래에서 설치된 폰트 설정
![](https://i.imgur.com/lvAsKKf.png)


## zsh-syntax-highlighting 설치하기
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc

```