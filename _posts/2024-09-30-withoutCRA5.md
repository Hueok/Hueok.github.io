---
title: "[withoutCRA5] CRA없이 React앱 구성하기 - Webpack plugin"
categories: [Front-end]
tags: [react, cra] # TAG names should always be lowercase
---

## Webpack Mode option

Webpack has `mode` props in config.

- `production` : More build time. Code Optimization process. Tree Shaking. Code spliting. Code obfuscation. Constant Folding. etc..

- `developement` : No

`mode: "$mode"`


## Plugin

빌드 프로세스에서 추가 기능을 수행하는 확장 모듈

## html-webpack-plugin

html에서 불러오고자 하는 스크립트 파일의 이름이 바뀌면, 자동으로 그에 맞게 수정해주는 플러그인

```shell
$ npm install --save-dev html-webpack-plugin
```

### Usage

```js
const HtmlWebpackPlugin = require("html-webpack-plugin")
...
plugins: [
    new HtmlWebpackPlugin({
        template: "./index.html", //기준 html 파일명
        filename: "index.html", //output filename
    }),
]
...
```