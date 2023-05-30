# Vue.js

[데모](https://github.com/MaximHelio/vuedongsan)

## Install

[https://nodejs.org](https://nodejs.org/)

## NVM (Node Version Manager)

Node.js 버전을 관리하는 프로그램, 어느 버전이든 설치, 변경, 삭제 가능하다.

#### Mac OS

https://github.com/nvm-sh/nvm

https://gist.github.com/falsy/8aa42ae311a9adb50e2ca7d8702c9af1

```bash
# 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

# vi 에디터 실행
vi ~/.zshrc

# 해당 경로 적용 시키키
# nvm
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

# ~/.zshrc 재 실행 시키기
source ~/.zshrc

# .bash인 경우
# vi ~/.bash_profile
# source ~/.bash_profile
```

#### Windows

https://github.com/coreybutler/nvm-windows/releases

```bash
# 설치 된 node.js 리스트 보기
nvm ls

# 해당 버전을 설치
nvm install 16.18.1

# 해당 버전을 삭제
nvm uninstall 16.18.1

# 해당 버전을 사용
nvm use 16.18.1

# 기본 버전 변경
nvm alias default 16.18.1
```

## Vue CLI

https://kr.vuejs.org/v2/guide/index.html

https://cli.vuejs.org/guide/installation.html

```bash
# Vue CLI 설치
npm install -g @vue/cli

# Vue.js 프로젝트 생성
vue create vue-study
## Normal type without Test ([Vue 2] dart-sass, babel, router, vuex, eslint) 선택

cd vue-study
code .

# VSCode로 해당 디렉토리 열기
## build
npm run build
npm install -g serve
serve -s dist

## 프로젝트 실행
npm run serve
```

## 필요없는 파일 지우기

```
- src/assets/logo.png
- src/views (폴더 삭제)
- src/components/HelloWorld.vue
```











# 다음

- [nuxt.js](../Nuxt-curriculum/README.md)
