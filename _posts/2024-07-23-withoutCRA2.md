---
title: "[withoutCRA2] CRA없이 React앱 구성하기"
categories: [Front-end]
tags: [react, cra] # TAG names should always be lowercase
---

### Use Babel
In this chapter, we use transfer(babel). Babel also available in form of CDN.

```html
...
<script src="https://unpkg.com/@babel/standalone@7.24.10/babel.min.js"></script>
...
```
we know simply adding this line is okay.

![img](/images/withoutCRA2_img/babelLoad.png)

We can see Babel perfectly loaded. It means from now we can use JSX syntax also.

```html
...
const App = () => <h1>Hello React World with JSX!</h1>;
ReactDOM.render(<App />, document.getElementById("root"));
...
```

But, still contrary to expectations, syntax error is occured
![img](/images/withoutCRA2_img/stillError.png)

-> why? : because we didn't provide to a browser, what script will be transfiled. We can solve it by adding `type="text/babel"` in `<script>` tag.

```html 
<script type="text/babel"> //let browser know "this part should be transfiled"
```

Now, we can see expected result.
![img](/images/withoutCRA2_img/JSXrender.png)

But At console, we can see warning message as below.
![img](/images/withoutCRA2_img/babelWarning.png)
It means we should execute pre-transfered script instead of transferring in browser.

---

At next chapter, we'll solve this warning. -> `Webpack`