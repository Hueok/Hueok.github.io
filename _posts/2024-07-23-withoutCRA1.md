---
title: "[withoutCRA1] CRA없이 React앱 구성하기"
categories: [Front-end]
tags: [react, cra] # TAG names should always be lowercase
---

### Lets develope a very primitive style react app without Babel and Webpack in this chapter

first, simply setup `index.html` file with basic html structure

Use CDN to load `React` and `ReactDom` packages

> why? -> to setup the simplest form of react app

> [Unpkg.com](https://unpkg.com/) provide packages as form of cdn which is registered in npm

```html
<!DOCTYPE html>
<html>
  <head>
    <title>React App without CRA</title>
    <script src="https://unpkg.com/react@18.3.1/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.production.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

Now, we can see script is loaded properly in Network tab
![img-description](/images/withoutCRA1_img/cdnLoad.png)

Also, we can check in console React and ReactDOM is properly assigned to global object
![img-description](/images/withoutCRA1_img/globalObj.png)

---
 
#### Using CRA, like below `index.js` is made basically.


```js
//index.js
import React from "react";
import ReactDOM from "react-dom/client";
import "./index.css";
import App from "./App";
import reportWebVitals from "./reportWebVitals";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);

reportWebVitals();
```

Now, lets apply it to `<script>` tag in `index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <title>React App without CRA</title>
    <script src="https://unpkg.com/react@18.3.1/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18.3.1/umd/react-dom.production.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script>
      const App = () => {
        return <h1>HELLO REACT WORLD</h1>;
      };
      const root = ReactDOM.createRoot(document.getElementById("root"));
      root.render(
        <React.StrictMode>
          <App />
        </React.StrictMode>
      );
    </script>
  </body>
</html>
```

> - define App component
> - show text in h1 tag
> - find element which has id 'root' in index.html
> - and assign it to variable 'root'
> - then, render <App /> component in root

<br />

But, contrary to expectations, syntax error is occured
![img](/images/withoutCRA1_img/syntaxError.png)

-> Why this error is occured? : `<tag>` syntax is not traditional syntax for **javascript**.
<br />
This is JSX syntax. So we should create component without using JSX -> `React::createElement` function help it.
> `createElement(tag, props, children)` : props can be `null`


This is modified code part:
```html
...
<script>
  const App = () => React.createElement('h1', null, "Hello React World!");
  ReactDOM.render(
    React.createElement(App),
    document.getElementById("root")
  );
</script>
...
```
<br />

Now, as we expected, message is shown well.

![img](/images/withoutCRA1_img/helloReact.png){: .normal }

---
#### So far, we made the simplest form of React application without Babel and Webpack.

#### As we know at now, we can make react applicatoin without JSX. but, more complex applicatoin, the more difficult to define component.

#### Then, how can we use JSX syntax? ->  Transpiler!(ex Babel) : transfer modern js syntax(include JSX) to old one.

#### At next post, let's use JSX instead of `createElement` function
