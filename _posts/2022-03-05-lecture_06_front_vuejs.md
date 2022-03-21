---
layout: single
title:  "[Lecture] - vueJS 설치 및 BackEnd 연계"
excerpt: "Windows환경 VueJS 설치 및 BackEnd 연계방법"

categories:
  - Lecture
tags:
  - [Lecture, Java, Servlet]

toc: true
toc_sticky: true
 
date: 2022-03-21
last_modified_at: 2022-03-21
---
# vueJS 개발환경 및 BadkEnd 연동
## 1. vueJS 개발환경
### 1-1. VSCode 설치
#### 플러그인 설치
- [공통]
  - Auto close Tag : 자동 태그 닫기
  - Color Highlight : 컬러 색상 미리보기
  - Live Server : 코딩을 실시간으로 확인-적용시켜 작업하기 편리
  - One dark pro : 어두운 테마로 작성하는 코딩이 눈에 잘 띔
  - Korean Language Pack for Visual Studio Code : VSCode 한국어 지원
  - Prettier : 코드 자동 정렬 ( ** 추후 추가 설정 필요 **)
  - ESLint  : 소스코드를 분석해 문법 에러, 버그를 찾기 ( ** 추후 추가 설정 필요 **)
  - vscode-icons : VSCode 내 파일의 아이콘을 설정하여 무슨 파일인지 한번에 파악가능
  - project manager 

- [ Vue관련 ]
  - Vetur : 문법 강조, vue.js 파일 코드의 하이라이팅 지원
  - Vue VSCode Snippets : 코드 조작 지원, vue.js 파일 초기 구성 생성
  - vue-beautify : 코드정렬
  - Vetur Extension
  - Vue 2 Snippets Extension
  - Vue 3 Snippets Extension
  - Vue peek

### 1-2. nodeJS 환경구축
- https://nodejs.org/ko/
- NodeJS를 설치하면 자동으로 npm(NodeJS Package Manager)도 함께 설치되므로 별도로 설치할 필요는 없다. npm이란 자바스크립트 기반의 패키지를 쉽게 설치 및 관리할 수 있도록 도와주는 툴.

```
PS C:\09_BLOG> node -v
v16.14.1
PS C:\09_BLOG> npm -v
8.5.0
```

### 1-3. Vue Devtools 설치
- Vue Devtools는 웹 브라우저인 크롬이나 파이어폭스에서 사용할 수 있는 확장애플리케이션.
- Vue를 사용한 애플리케이션을 개발할 때 도움을 주는 유용한 툴로서, 애플리케이션의 구조 및 데이터의 흐름을 디버깅할 때 유용하다. 별도로 설정을 변경하지 않으면 개발용 빌드에서는 사용할 수 있지만 배포용 빌드에서는 사용할 수 없다

### 1-4. Vue CLI 설치
- Vue CLI의 설치를 위해서 먼저 터미널 혹은 CMD에 설치 명령어를 입력한다.

> $ npm install vue-cli -g

> npm WARN deprecated har-validator@5.1.5: this library is no longer supported
> npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
> npm WARN deprecated request@2.88.2: request has been deprecated, see https://github.com/request/request/issues/3142
> npm WARN deprecated coffee-script@1.12.7: CoffeeScript on NPM has moved to "coffeescript" (no hyphen)
> npm WARN deprecated vue-cli@2.9.6: This package has been deprecated in favour of @vue/cli
> 
> added 245 packages, and audited 246 packages in 9s
> 
> 11 packages are looking for funding
>   run `npm fund` for details
> 
> 4 moderate severity vulnerabilities
> 
> To address all issues (including breaking changes), run:
>   npm audit fix --force
> 
> Run `npm audit` for details.
>  
> 
> PS C:\09_BLOG> npm install vue-cli -g
> npm WARN deprecated har-validator@5.1.5: this library is no longer supported
> 142
> npm WARN deprecated coffee-script@1.12.7: CoffeeScript on NPM has moved to "coffeescript" (no hyphen)
> npm WARN deprecated vue-cli@2.9.6: This package has been deprecated in favour of @vue/cli
> 
> added 245 packages, and audited 246 packages in 9s
> 
> 11 packages are looking for funding
>   run `npm fund` for details
> 
> 4 moderate severity vulnerabilities
> 
> To address all issues (including breaking changes), run:
>   npm audit fix --force
> 
> Run `npm audit` for details.
> PS C:\09_BLOG>
> PS C:\09_BLOG> vue -V
> vue : 이 시스템에서 스크립트를 실행할 수 없으므로 C:\Users\m2m-129\AppData\Roaming\npm\vue.ps1 파일을 로드할 수
>  없습니다. 자세한 내용은 about_Execution_Policies(https://go.microsoft.com/fwlink/?LinkID=135170)를 참조하십시
> + ~~~
>     + CategoryInfo          : 보안 오류: (:) [], PSSecurityException
>     + FullyQualifiedErrorId : UnauthorizedAccess
> PS C:\09_BLOG>
> 
> PS C:\09_BLOG> vue.cmd create vue-cli
> 
>   vue create is a Vue CLI 3 only command and you are using Vue CLI 2.9.6.
>   You may want to run the following to upgrade to Vue CLI 3:
> 
>   npm uninstall -g vue-cli
>   npm install -g @vue/cli
> 
> PS C:\09_BLOG> get-executionpolicy
> Restricted
> PS C:\09_BLOG> set-executionpolicy remotesigned
> PS C:\09_BLOG> get-executionpolicy
> RemoteSigned
> PS C:\09_BLOG> vue --version
> @vue/cli 5.0.3
> PS C:\09_BLOG> vue -V
> @vue/cli 5.0.3  
> PS C:\09_BLOG> vue init webpack m2mVueJSDemo
> 
> ? Project name m2m-vuejs-demo
> ? Project description m2mglobal vueJS Demo
> ? Author m2m
> ? Vue build standalone
> ? Install vue-router? Yes
> ? Use ESLint to lint your code? Yes
> ? Pick an ESLint preset Standard
> ? Set up unit tests No
> ? Setup e2e tests with Nightwatch? No
> ? Should we run `npm install` for you after the project has been created? (recommended) npm
> 
>    vue-cli · Generated "m2mVueJSDemo".
> 
> 
> # Installing project dependencies ...
> # ========================
> 
> npm WARN deprecated source-map-url@0.4.1: See https://github.com/lydell/source-map-url#deprecated
> npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
> npm WARN deprecated source-map-resolve@0.5.3: See https://github.com/lydell/source-map-resolve#deprecated
> npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
> npm WARN deprecated flatten@1.0.3: flatten is deprecated in favor of utility frameworks such as lodash.
> npm WARN deprecated circular-json@0.3.3: CircularJSON is in maintenance only, flatted is its successor.
> npm WARN deprecated eslint-loader@1.9.0: This loader has been deprecated. Please use eslint-webpack-plugin
> npm WARN deprecated uuid@3.4.0: Please upgrade  to version 7 or higher.  Older versions may use Math.random() in certain circumstances, which is known to be problematic.  See https://v8.dev/blog/math-random for details.
> npm WARN deprecated querystring@0.2.0: The querystring API is considered Legacy. new code should use the URLSearchParams API instead.
> npm WARN deprecated browserslist@2.11.3: Browserslist 2 could fail on reading Browserslist >3.0 config used in other tools.
> npm WARN deprecated browserslist@1.7.7: Browserslist 2 could fail on reading Browserslist >3.0 config used in other tools.
> npm WARN deprecated extract-text-webpack-plugin@3.0.2: Deprecated. Please use https://github.com/webpack-contrib/mini-css-extract-plugin
> npm WARN deprecated browserslist@1.7.7: Browserslist 2 could fail on reading Browserslist >3.0 config used in other tools.
> npm WARN deprecated html-webpack-plugin@2.30.1: out of support
> npm WARN deprecated chokidar@2.1.8: Chokidar 2 does not receive security updates since 2019. Upgrade to chokidar 3 with 15x fewer dependencies
> npm WARN deprecated browserslist@1.7.7: Browserslist 2 could fail on reading Browserslist >3.0 config used in other tools.
> npm WARN deprecated babel-eslint@8.2.6: babel-eslint is now @babel/eslint-parser. This package will no longer receive updates.
> npm WARN deprecated uglify-es@3.3.9: support for ECMAScript is superseded by `uglify-js` as of v3.13.0
> npm WARN deprecated chokidar@2.1.8: Chokidar 2 does not receive security updates since 2019. Upgrade to chokidar 3 with 15x fewer dependencies
> npm WARN deprecated bfj-node4@5.3.1: Switch to the `bfj` package for fixes and new features!
> npm WARN deprecated svgo@0.7.2: This SVGO version is no longer supported. Upgrade to v2.x.x.
> npm WARN deprecated svgo@1.3.2: This SVGO version is no longer supported. Upgrade to v2.x.x.
> npm WARN deprecated core-js@2.6.12: core-js@<3.4 is no longer maintained and not recommended for usage due to the number of issues. Because of the V8 engine whims, feature detection in old core-js versions could cause a slowdown up to 100x even if nothing is polyfilled. Please, upgrade your dependencies to the actual version of core-js.
> 
> added 1437 packages, and audited 1438 packages in 38s
> 
> 64 packages are looking for funding
>   run `npm fund` for details
> 
> 84 vulnerabilities (68 moderate, 16 high)
> 
> To address issues that do not require attention, run:
>   npm audit fix
> 
> To address all issues (including breaking changes), run:
>   npm audit fix --force
> 
> Run `npm audit` for details.
> 
> 
> Running eslint --fix to comply with chosen preset rules...
> # ========================
> 
> 
> > m2m-vuejs-demo@1.0.0 lint
> > eslint --ext .js,.vue src "--fix"
> 
> 
> # Project initialization finished!
> # ========================
> 
> To get started:
> 
>   cd m2mVueJSDemo
>   npm run dev
> 


