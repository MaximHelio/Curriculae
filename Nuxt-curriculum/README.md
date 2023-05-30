# Nuxt.js

> - 주로 Vue.js의 SPA(Single page application)의 단점인 SEO(Search engine optimization)을 해결하기 위해 사용한다.
> - Express 서버의 SSR(Server side rendering)과, Vue.js의 CSR(Client side rendering)을 혼합하여 사용해서, SEO를 해결 한다.

- [데모](https://github.com/MaximHelio/Nuxt-pjt)

## 설치

```bash
# Nuxt 프로젝트 생성
npx create-nuxt-app nuxt-study
  Project name: nuxt-study
  Programming language: JavaScript
  Package manager: Npm
  UI framework: None
  Nuxt.js modules: Axios - Promise based HTTP client <-선택
  Linting tools: 선택 안함
  Testing framework: None or Jest
  Rendering mode: Universal (SSR / SSG)
  Deployment target: Server (Node.js hosting)
  Development tools: jsconfig.json (Recommended for VS Code if you're not using typescript)
  Continuous integration: None
  Version control system: Git

cd nuxt-study
code .

# 결과물을 .nuxt 경로에 build
npm run build
# build된 .nuxt 경로를 로컬 서버로 띄운다.
npm run start

# 로컬 개발 서버를 띄우기
npm run dev
```