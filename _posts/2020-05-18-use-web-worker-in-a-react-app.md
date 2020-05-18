---
layout: post
title: 如何在一个React应用中使用Web Worker
summary: 发现在React应用中使用Web Worker需要做些额外工作才能正常运行，一起来看看！
featured-img: react
categories: React
---


今天闲着没事，于是想试一下在React应用中使用Web Worker，用来测试Web Worker运行在单独的线程中，不影响主线程。

于是我用Create React App生成了一个React初始工程，然后在`src`目录添加了一个文件`worker.js`：

```javascript
let i = 0;
while (i < 200000) {
  postMessage("Web Worker Counter: " + i);
  i++;
}
```

接着，我在`App.js`写了一些简单的代码：

```javascript
import React, { useState } from "react";
import "./App.css";

let worker;

function App() {
  const [workerOutput, setWorkerOutput] = useState("");
  const [mainThreadOutput, setMainThreadOutput] = useState("");

  const testWorker = () => {
    if (typeof Worker !== "undefined") {
      if (typeof worker === "undefined") {
        worker = new Worker('./worker.js');
      }
      worker.onmessage = function (event) {
        setWorkerOutput(event.data);
      };
    } else {
      setWorkerOutput("Web Workers are not supported in your browser");
    }
  };

  const terminateWorker = () => {
    worker && worker.terminate();
    worker = undefined;
  };

  const testMainThread = () => {
    for (let i = 0; i < 2000000; i++) {
      setMainThreadOutput("Main Thread Counter: " + i);
    }
  };

  return (
    <div className="App">
      <div className="output-cont">
        <button onClick={testWorker}>start worker</button>
        <h3 id="workerOutput">{workerOutput}</h3>
        <button onClick={terminateWorker}>terminate worker</button>
      </div>
      <br />
      <div className="output-cont">
        <button onClick={testMainThread}>start blocking main thread</button>
        <h3 id="mainThreadOutput">{mainThreadOutput}</h3>
      </div>
      <br />
      <div className="output-cont">
        <button
          onClick={() => {
            alert("browser responsive!");
          }}
        >
          test browser responsiveness
        </button>
      </div>
    </div>
  );
}

export default App;
```

我想测试当Web Worker中执行一些繁重的计算工作的时候，用户依然可以和页面进行交互，比如响应用户点击事件。

一切看起来都很完美！

但是当我点击`start worker`按钮的时候，浏览器报告了一个错误：

```
Uncaught SyntaxError: Unexpected token '<'
```

于是我去网上寻找解决方案，最终在Create React App的GitHub仓库找到了[方法](https://github.com/facebook/create-react-app/issues/1277#issuecomment-292708809)。

照着他的方法，我把代码改成了这样：

```javascript
const workerCode = () => {
  let i = 0;
  while (i < 200000) {
    postMessage("Web Worker Counter: " + i);
    i++;
  }
};

let code = workerCode.toString();
code = code.substring(code.indexOf('{') + 1, code.lastIndexOf('}'));

const blob = new Blob([code], { type: 'application/javascript'});
const worker_script = URL.createObjectURL(blob);

module.exports = worker_script;
```

```javascript
import React, { useState } from "react";
import "./App.css";
import worker_script from './worker'; <--- 导入

let worker;

function App() {
  const [workerOutput, setWorkerOutput] = useState("");
  const [mainThreadOutput, setMainThreadOutput] = useState("");

  const testWorker = () => {
    if (typeof Worker !== "undefined") {
      if (typeof worker === "undefined") {
        worker = new Worker(worker_script); <--- 使用
      }
      worker.onmessage = function (event) {
        setWorkerOutput(event.data);
      };
    } else {
      setWorkerOutput("Web Workers are not supported in your browser");
    }
  };

  ...剩余代码
}

export default App;
```

再次运行程序，这一次终于正常了。

让我们逐步看一下做了哪些修改：

```javascript
const workerCode = () => {
  let i = 0;
  while (i < 200000) {
    postMessage("Web Worker Counter: " + i);
    i++;
  }
};
```

↑首先把要在Worker中执行的代码放到一个函数里面。

```javascript
let code = workerCode.toString();
```

↑接着把函数转为字符串。

```javascript
code = code.substring(code.indexOf('{') + 1, code.lastIndexOf('}'));
```

↑然后截取函数体部分。

```javascript
const blob = new Blob([code], { type: 'application/javascript'});
```

↑把这部分字符串转为[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)对象。

```javascript
const worker_script = URL.createObjectURL(blob);
```

↑再把这个Blob对象转为[DOMString](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMString)。

这样，就可以在别的文件通过`import`来访问这个文件对象了：

```javascript
import worker_script from './worker';
```

由于本人水平有限，对这里面的具体技术细节还不是很了解，等我熟悉这里面的细节，我会回来更新这篇文章。

感谢阅读！