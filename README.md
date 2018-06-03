- 这个repo是一个简单的Webpack演示的集合。

这些演示故意写成简单明了的风格。 你会发现在学习强大工具方面没有任何困难。

## 如何使用

首先，全局安装[Webpack](https://www.npmjs.com/package/webpack)和[webpack-dev-server](https://www.npmjs.com/package/webpack-dev-server)。

```bash
$ npm i -g webpack webpack-dev-server
```

然后，克隆repo。

```bash
$ git clone https://github.com/ruanyf/webpack-demos.git
```

安装依赖关系。

```bash
$ cd webpack-demos
$ npm install
```

现在，使用repo的demo *目录下的源文件。

```bash
$ cd demo01
$ npm run dev
```

如果上述命令不能自动打开浏览器，则必须自己访问http://127.0.0.1:8080。

## 前言：什么是Webpack

Webpack是为浏览器构建JavaScript模块脚本的前端工具。

它可以使用类似于Browserify，并做更多。

```bash
$ browserify main.js > bundle.js
# be equivalent to
$ webpack main.js bundle.js
```

Webpack需要一个名为`webpack.config.js`的配置文件，它只是一个CommonJS模块。

```javascript
// webpack.config.js
module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  }
};
```

拥有`webpack.config.js`后，您可以在不带任何参数的情况下调用Webpack。

```bash
$ webpack
```

一些你应该知道的命令行选项。

- `webpack` – building for development
- `webpack -p` – building for production (minification)
- `webpack --watch` – for continuous incremental building
- `webpack -d` – including source maps
- `webpack --colors` – making building output pretty

你可以在你的package.json文件中定制`scripts`字段，如下所示。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "webpack-dev-server --devtool eval --progress --colors",
    "deploy": "NODE_ENV=production webpack -p"
  },
  // ...
}
```

## Index

1. [Entry file](#demo01-entry-file-source)
1. [Multiple entry files](#demo02-multiple-entry-files-source)
1. [Babel-loader](#demo03-babel-loader-source)
1. [CSS-loader](#demo04-css-loader-source)
1. [Image loader](#demo05-image-loader-source)
1. [CSS Module](#demo06-css-module-source)
1. [UglifyJs Plugin](#demo07-uglifyjs-plugin-source)
1. [HTML Webpack Plugin and Open Browser Webpack Plugin](#demo08-html-webpack-plugin-and-open-browser-webpack-plugin-source)
1. [Environment flags](#demo09-environment-flags-source)
1. [Code splitting](#demo10-code-splitting-source)
1. [Code splitting with bundle-loader](#demo11-code-splitting-with-bundle-loader-source)
1. [Common chunk](#demo12-common-chunk-source)
1. [Vendor chunk](#demo13-vendor-chunk-source)
1. [Exposing Global Variables](#demo14-exposing-global-variables-source)
1. [React router](#demo15-react-router-source)

## Demo01: Entry file ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo01))

入口文件是Webpack用来读取以 `bundle.js` 构建的文件。

例如，`main.js`是一个入口文件

```javascript
// main.js
document.write('<h1>Hello World</h1>');
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

Webpack遵循`webpack.config.js`来构建`bundle.js`。

```javascript
// webpack.config.js
module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  }
};
```

启动服务器，请访问http://127.0.0.1:8080。

```bash
$ cd demo01
$ npm run dev
```

## Demo02: Multiple entry files ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo02))

多个入口文件是允许的。 对于每个页面具有不同入口文件的多页面应用程序非常有用

```javascript
// main1.js
document.write("<h1>Hello World</h1>");

// main2.js
document.write("<h2>Hello Webpack</h2>");
```

index.html

```html
<html>
  <body>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: {
    bundle1: "./main1.js",
    bundle2: "./main2.js"
  },
  output: {
    filename: "[name].js"
  }
};
```

## Demo03: Babel-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo03))

装载机是预处理程序，它在Webpack构建过程之前转换应用程序的资源文件（[更多信息]（http://webpack.github.io/docs/using-loaders.html））。

例如，[Babel-loader](https://www.npmjs.com/package/babel-loader)可以将JSX / ES6文件转换为正常的JS文件，之后Webpack将开始构建这些JS文件。 Webpack的官方文档有一个完整的[loader](http://webpack.github.io/docs/list-of-loaders.html)列表。

`main.jsx`是一个JSX文件。

```javascript
// main.jsx
const React = require("react");
const ReactDOM = require("react-dom");

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector("#wrapper")
);
```

index.html

```html
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: "./main.jsx",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015", "react"]
          }
        }
      }
    ]
  }
};
```

上面的代码片段使用`babel-loader`，它需要Babel的预设插件[babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015)和[babel-preset-react](https ：//www.npmjs.com/package/babel-preset-react)来转储ES6和React。

## Demo04: CSS-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo04))

Webpack允许你在JS文件中包含CSS，然后用[CSS-loader](https://github.com/webpack-contrib/css-loader)预处理CSS文件。

main.js

```javascript
require("./app.css");
```

app.css

```css
body {
  background-color: blue;
}
```

index.html

```html
<html>
  <head>
    <script type="text/javascript" src="bundle.js"></script>
  </head>
  <body>
    <h1>Hello World</h1>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use: [ "style-loader", "css-loader" ]
      },
    ]
  }
};
```

注意，你必须使用两个加载器来转换CSS文件。 首先是[CSS-loader](https://www.npmjs.com/package/css-loader)读取CSS文件，另一个是[Style-loader](https://www.npmjs.com/package/style-loader)将`<style>`标签插入HTML页面。

然后，启动服务器。

```bash
$ cd demo04
$ npm run dev
```

实际上，Webpack将一个内部样式表插入`index.html`中。

```html
<head>
  <script type="text/javascript" src="bundle.js"></script>
  <style type="text/css">
    body {
      background-color: blue;
    }
  </style>
</head>
```

## Demo05: Image loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo05))

Webpack也可以在JS文件中包含图像。

main.js

```javascript
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html

```html
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules:[
      {
        test: /\.(png|jpg)$/,
        use: [
          {
            loader: "url-loader",
            options: {
              limit: 8192
            }
          }
        ]
      }
    ]
  }
};
```

[url-loader](https://www.npmjs.com/package/url-loader)将图像文件转换为`<img>`标签。 如果图像大小小于8192字节，则会转换为数据URL; 否则，它将被转换成正常的URL。

启动服务器后，`small.png`和`big.png`具有以下URL。

```html
<img src="data:image/png;base64,iVBOR...uQmCC">
<img src="4853ca667a2b8b8844eb2693ac1b2578.png">
```

## Demo06: CSS Module ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo06))

`css-loader？modules`（查询参数模块）启用[CSS模块](https://github.com/css-modules/css-modules)，它将本地范围的CSS提供给您的JS模块的CSS。 你可以用：`global（selector）`（[更多信息](https://css-modules.github.io/webpack-demo)））将其关闭。

index.html

```html
<html>
<body>
  <h1 class="h1">Hello World</h1>
  <h2 class="h2">Hello Webpack</h2>
  <div id="example"></div>
  <script src="./bundle.js"></script>
</body>
</html>
```

app.css

```css
/* local scope */
.h1 {
  color:red;
}

/* global scope */
:global(.h2) {
  color: blue;
}
```

main.jsx

```javascript
var React = require("react");
var ReactDOM = require("react-dom");
var style = require("./app.css");

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById("example")
);
```

webpack.config.js

```javascript
module.exports = {
  entry: "./main.jsx",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015", "react"]
          }
        }
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: "style-loader"
          },
          {
             loader: "css-loader",
             options: {
               modules: true
             }
          }
        ]
      }
    ]
  }
};
```

启动服务器。

```bash
$ cd demo06
$ npm run dev
```

访问http://127.0.0.1:8080，
你会发现只有第二个`h1`是红色的，因为它的CSS是局部范围的，并且`h2`都是蓝色的，因为它的CSS是全局范围的。

## Demo07: UglifyJs Plugin ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo07))

Webpack有一个插件系统来扩展它的功能。 例如，[UglifyJs Plugin](https://webpack.js.org/plugins/uglifyjs-webpack-plugin/)将缩小输出（`bundle.js`）JS代码。

main.js

```javascript
var longVariableName = "Hello";
longVariableName += " World";
document.write("<h1>" + longVariableName + "</h1>");
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require("webpack");
var UglifyJsPlugin = require("uglifyjs-webpack-plugin");

module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new UglifyJsPlugin()
  ]
};
```

启动服务器后，`main.js`将被缩小为以下内容。

```javascript
var o="Hello";o+=" World",document.write("<h1>"+o+"</h1>")
```

Demo08：HTML Webpack插件和开放浏览器Webpack插件（[source](https://github.com/ruanyf/webpack-demos/tree/master/demo08)）

本演示向您展示了如何加载第三方插件。

[html-webpack-plugin](https://github.com/ampedandwired/html-webpack-plugin)可以为你创建`index.html`，并且[open-browser-webpack-plugin](https：// github .com / baldore / open-browser-webpack-plugin)可以在Webpack加载时打开一个新的浏览器选项卡。

main.js

```javascript
document.write("<h1>Hello World</h1>");
```

webpack.config.js

```javascript
var HtmlwebpackPlugin = require("html-webpack-plugin");
var OpenBrowserPlugin = require("open-browser-webpack-plugin");

module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new HtmlwebpackPlugin({
      title: "Webpack-demos",
      filename: "index.html"
    }),
    new OpenBrowserPlugin({
      url: "http://localhost:8080"
    })
  ]
};
```

启动服务器。

```bash
$ cd demo08
$ npm run dev
```

现在你不需要手动编写`index.html`，也不需要自己打开浏览器。 Webpack为你做了所有这些事情。

## Demo09: Environment flags ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo09))

您只能在具有环境标志的开发环境中启用一些代码。

main.js

```javascript
document.write("<h1>Hello World</h1>");

if (__DEV__) {
  document.write(new Date());
}
```

index.html

```html
<html>
<body>
  <script src="bundle.js"></script>
</body>
</html>
```

webpack.config.js

```javascript
var webpack = require("webpack");

var devFlagPlugin = new webpack.DefinePlugin({
  __DEV__: JSON.stringify(JSON.parse(process.env.DEBUG || "false"))
});

module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  },
  plugins: [devFlagPlugin]
};
```

现在将环境变量传递给webpack。 打开`demo09 / package.json`，你应该找到`scripts`字段，如下所示。

```javascript
// package.json
{
  // ...
  "scripts": {
    "dev": "cross-env DEBUG=true webpack-dev-server --open",
  },
  // ...
}
```

启动服务器。

```javascript
$ cd demo09
$ npm run dev
```

## Demo10: Code splitting ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo10))

对于大型Web应用程序，将所有代码放入单个文件是无效的。 Webpack允许你将一个大的JS文件分成几个块。 尤其是，如果在某些情况下只需要某些代码块，则可以根据需要加载这些块。

Webpack使用`require.ensure`来定义分割点（[官方文档](http://webpack.github.io/docs/code-splitting.html)）。

```javascript
// main.js
require.ensure(["./a"], function (require) {
  var content = require("./a");
  document.open();
  document.write("<h1>" + content + "</h1>");
  document.close();
});
```

`require.ensure`告诉Webpack`.a.js`应该从`bundle.js`中分离出来并且构建成一个块文件。

```javascript
// a.js
module.exports = "Hello World";
```

现在，Webpack负责处理依赖关系，输出文件和运行时间。 你不必在你的`index.html`和`webpack.config.js`中放入任何冗余。

```html
<html>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
module.exports = {
  entry: "./main.js",
  output: {
    filename: "bundle.js"
  }
};
```

启动服务器。

```bash
$ cd demo10
$ npm run dev
```

表面上，你不会感觉到任何差异。 然而，Webpack实际上将`main.js`和`a.js`构建为不同的块（`bundle.js`和`0.bundle.js`），当需要时从`bundle.js`加载`0.bundle.js` 。

## Demo11: Code splitting with bundle-loader ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo11))

另一种代码拆分方式是使用[bundle-loader](https://www.npmjs.com/package/bundle-loader)。

```javascript
// main.js

//现在请求a.js，它将被捆绑到另一个文件中
var load = require("bundle-loader!./a.js");

//要等到a.js可用（并获得导出），您需要异步等待它。
load(function(file) {
  document.open();
  document.write("<h1>" + file + "</h1>");
  document.close();
});
```

`require（"bundle-loader！./a.js"）`告诉Webpack从另一个块加载`a.js`。

现在，Webpack将把`main.js`构建成`bundle.js`，将`a.js`构建成`0.bundle.js`。

## Demo12: Common chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo12))

当多脚本具有常用块时，可以使用[CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)将公共部分提取到单独的文件中，这对浏览器缓存和节省带宽很有用。

```javascript
// main1.jsx
var React = require("react");
var ReactDOM = require("react-dom");

ReactDOM.render(
  <h1>Hello World</h1>,
  document.getElementById("a")
);

// main2.jsx
var React = require("react");
var ReactDOM = require("react-dom");

ReactDOM.render(
  <h2>Hello Webpack</h2>,
  document.getElementById("b")
);
```

index.html

```html
<html>
  <body>
    <div id="a"></div>
    <div id="b"></div>
    <script src="commons.js"></script>
    <script src="bundle1.js"></script>
    <script src="bundle2.js"></script>
  </body>
</html>
```

上面的`commons.js`是`main1.jsx`和`main2.jsx`的常见块。 你可以想象，commons.js包含了"react"和"react-dom"。

webpack.config.js

```javascript
var webpack = require("webpack");

module.exports = {
  entry: {
    bundle1: "./main1.jsx",
    bundle2: "./main2.jsx"
  },
  output: {
    filename: "[name].js"
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015", "react"]
          }
        }
      },
    ]
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: "commons",
      // (公用块名称)

      filename: "commons.js",
      // (公用块的文件名)
    })
  ]
}
```

## Demo13: Vendor chunk ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo13))

您还可以将脚本中的供应商库从CommonsChunkPlugin中提取到单独的文件中。

main.js

```javascript
var $ = require("jquery");
$("h1").text("Hello World");
```

index.html

```html
<html>
  <body>
    <h1></h1>
    <script src="vendor.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```javascript
var webpack = require("webpack");

module.exports = {
  entry: {
    app: "./main.js",
    vendor: ["jquery"],
  },
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: "vendor",
      filename: "vendor.js"
    })
  ]
};
```

在上面的代码中，`entry.vendor：["jquery"]`告诉Webpack, `jquery`应该包含在公共块`vendor.js`中。

如果你想在每个模块中使用一个模块作为全局变量，比如在没有写入require（"jquery"）的情况下在每个模块中都可以使用`$`和`jQuery`。 你应该使用`ProvidePlugin`（[官方文档](https://webpack.js.org/plugins/provide-plugin/)），它可以自动加载模块，而不必在任何地方导入或需要它们。

```javascript
// main.js
$("h1").text("Hello World");


// webpack.config.js
var webpack = require("webpack");

module.exports = {
  entry: {
    app: "./main.js"
  },
  output: {
    filename: "bundle.js"
  },
  plugins: [
    new webpack.ProvidePlugin({
      $: "jquery",
      jQuery: "jquery"
    })
  ]
};
```

当然，在这种情况下，您应该自己在全局加载`jquery.js`。

## Demo14: Exposing global variables ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo14))

如果你想使用一些全局变量，并且不想将它们包含在Webpack包中，你可以在`webpack.config.js`中启用`externals`字段（[official document](https://webpack.js.org/configuration/externals/)）。

例如，我们有一个`data.js`。

```javascript
// data.js
var data = "Hello World";
```

index.html

```html
<html>
  <body>
    <script src="data.js"></script>
    <script src="bundle.js"></script>
  </body>
</html>
```

注意，Webpack只会建立`bundle.js`，但不会`data.js`。

我们可以将`data`暴露为全局变量。

```javascript
// webpack.config.js
module.exports = {
  entry: "./main.jsx",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015", "react"]
          }
        }
      },
    ]
  },
  externals: {
    // require（"data"）是外部的，可用于全局变量数据
    data: "data"
  }
};
```

现在，您需要`data`作为脚本中的模块变量。 但它实际上是一个全局变量。

```javascript
// main.jsx
var data = require("data");
var React = require("react");
var ReactDOM = require("react-dom");

ReactDOM.render(
  <h1>{data}</h1>,
  document.body
);
```

你也可以把`react`和`react-dom`放到`externals`中，这会大大减少`bundle.js`的建造时间和建筑尺寸。

## Demo15: React router ([source](https://github.com/ruanyf/webpack-demos/tree/master/demo15))

本演示使用webpack构建[React-router](https://github.com/rackt/react-router/blob/0.13.x/docs/guides/overview.md)的官方示例。

让我们想象一下带有仪表板，收件箱和日历的小应用程序。

```
+---------------------------------------------------------+
| +---------+ +-------+ +--------+                        |
| |Dashboard| | Inbox | |Calendar|      Logged in as Jane |
| +---------+ +-------+ +--------+                        |
+---------------------------------------------------------+
|                                                         |
|                        Dashboard                        |
|                                                         |
|                                                         |
|   +---------------------+    +----------------------+   |
|   |                     |    |                      |   |
|   | +              +    |    +--------->            |   |
|   | |              |    |    |                      |   |
|   | |   +          |    |    +------------->        |   |
|   | |   |    +     |    |    |                      |   |
|   | |   |    |     |    |    |                      |   |
|   +-+---+----+-----+----+    +----------------------+   |
|                                                         |
+---------------------------------------------------------+
```

webpack.config.js

```javascript
module.exports = {
  entry: "./index.js",
  output: {
    filename: "bundle.js"
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ "style-loader", "css-loader" ]
      },
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
          options: {
            presets: ["es2015", "react"]
          }
        }
      },
    ]
  }
};
```

index.js

```javascript
import React from "react";
import { render } from "react-dom";
import { BrowserRouter, Switch, Route, Link } from "react-router-dom";

import "./app.css";

class App extends React.Component {
  render() {
    return (
      <div>
        <header>
          <ul>
            <li><Link to="/app">Dashboard</Link></li>
            <li><Link to="/inbox">Inbox</Link></li>
            <li><Link to="/calendar">Calendar</Link></li>
          </ul>
          Logged in as Jane
        </header>
        <main>
          <Switch>
            <Route exact path="/" component={Dashboard}/>
            <Route path="/app" component={Dashboard}/>
            <Route path="/inbox" component={Inbox}/>
            <Route path="/calendar" component={Calendar}/>
            <Route path="*" component={Dashboard}/>
          </Switch>
        </main>
      </div>
    );
  }
};

class Dashboard extends React.Component {
  render() {
    return (
      <div>
        <p>Dashboard</p>
      </div>
    );
  }
};

class Inbox extends React.Component {
  render() {
    return (
      <div>
        <p>Inbox</p>
      </div>
    );
  }
};

class Calendar extends React.Component {
  render() {
    return (
      <div>
        <p>Calendar</p>
      </div>
    );
  }
};

render((
  <BrowserRouter>
    <Route path="/" component={App} />
  </BrowserRouter>
), document.querySelector('#app'));
```

index.html

```html
<html>
  <body>
    <div id="app"></div>
    <script src="/bundle.js"></script>
  </body>
</htmL>
```

启动服务器。

```bash
$ cd demo15
$ npm run dev
```

## Useful links

- [Webpack docs](https://webpack.js.org/concepts/)
- [webpack-howto](https://github.com/petehunt/webpack-howto), by Pete Hunt
- [SurviveJS Webpack book](https://survivejs.com/webpack/introduction/), by Juho Vepsäläinen
- [Diving into Webpack](https://web-design-weekly.com/2014/09/24/diving-webpack/), by Web Design Weekly
- [Webpack and React is awesome](http://www.christianalfoni.com/articles/2014_12_13_Webpack-and-react-is-awesome), by Christian Alfoni
- [Browserify vs Webpack](https://medium.com/@housecor/browserify-vs-webpack-b3d7ca08a0a9), by Cory House

## License

MIT
