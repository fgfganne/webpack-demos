# webpack学习笔记
## 重要地址
[webpack中文api](https://www.webpackjs.com/)

[webpack英文api](https://webpack.js.org)

[github上的webpack源码](https://github.com/webpack/webpack)

[慕课网"webpack深入与实战"](https://www.imooc.com/learn/802)

[阮一峰webpack-dmoes(推荐学习)](https://github.com/ruanyf/webpack-demos)

[《入门 Webpack，看这篇就够了》](https://segmentfault.com/a/1190000006178770)

## How to use

1、首先，全局安装webpck和webpack-dev-server
```bash
$ npm i -g webpack webpack-dev-server
```

2、然后 clone 这个git
```bash
$ git clone https://github.com/fgfganne/webpack-demos.git
```

3、安装依赖
```bash
$ cd webpack-study
$ npm install
```


# demos关键代码
## 1、Demo01: Entry file
```
module.exports = {
    entry:'./main.js',
    output:{
        filename:'./bundle.js'
    }
}
```

## 2、Demo02: Multiple entry files (注意'[name].js' 有引号)
```
    module.exports = {
        entry:{
            bundle1:'./main1.js', // 第1个JS文件
            bundle2:'./main2.js', // 第2个JS文件
        },
        output:{
            filename:'[name].js'  // 输出文件名
        }
    }
```

## 3、Demo03: Babel-loader 

加载器是预处理器，它可以在Webpack的构建过程之前转换你的应用程序的资源文件(更多信息)。

例如,Babel-loader可以JSX / ES6文件转换成标准JS文件,之后Webpack将开始构建这些JS文件。Webpack的官方文档有一个完整的加载器列表。

在html中，如果bundle.js在控件之前引入，会不显示内容。位置不可引错。

index.html
```
<html>
  <body>
    <div id="wrapper"></div>
    <script src="bundle.js"></script>
  </body>
</html>

```
`main.jsx` is a JSX file.
```
// main.jsx
const React = require('react');
const ReactDOM = require('react-dom');

ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.querySelector('#wrapper')
);
```

webpack.config.js
```
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      }
    ]
  }
};
```

## Demo04: CSS-loader

Webpack允许你在JS文件中包含CSS，然后用CSS-loader来预处理CSS文件。

注意，您必须使用两个加载器来转换CSS文件。首先是CSS-loader来读取CSS文件，另一个是样式加载器将<style>标签插入HTML中。
```
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ] //顺序不能颠倒
      },
    ]
  }
};
```
## Demo05: Image loader 

Webpack还可以在JS文件中包含图像

`main.js`
```bash
var img1 = document.createElement("img");
img1.src = require("./small.png");
document.body.appendChild(img1);

var img2 = document.createElement("img");
img2.src = require("./big.png");
document.body.appendChild(img2);
```

index.html  （js放在body中，或最后）
```bash
<html>
  <body>
    <script type="text/javascript" src="bundle.js"></script>
  </body>
</html>
```

webpack.config.js

```bash
module.exports = {
  entry: './main.js',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.(png|jpg)$/,
        use: [
          {
            loader: 'url-loader',
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

结果：
```
<img src="data:image/png;base64,iVBOR...uQmCC"> //体积小于8192，data url
<img src="4853ca667a2b8b8844eb2693ac1b2578.png"> //体积大于8192，正常url
```



## Demo06: CSS Module

index.html
```bash
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
```
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
```
var React = require('react');
var ReactDOM = require('react-dom');
var style = require('./app.css');

ReactDOM.render(
  <div>
    <h1 className={style.h1}>Hello World</h1>  //注意没有引号
    <h2 className="h2">Hello Webpack</h2>
  </div>,
  document.getElementById('example')
);
```

webpack.config.js
```
module.exports = {
  entry: './main.jsx',
  output: {
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.js[x]?$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['es2015', 'react']
          }
        }
      },
      {
        test: /\.css$/,
        use: [
          {
            loader: 'style-loader'
          },
          {
             loader: 'css-loader',
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

## 效果：
![avatar][demo06-img]













































[demo06-img]:data:image/png;base64,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











