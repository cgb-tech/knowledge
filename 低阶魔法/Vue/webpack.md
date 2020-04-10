## webpack安装

```
npm install webpack@版本号 -g 全局安装
npm install webpack@版本号 --save-dev 局部安装
npm init 初始化
npm install 安装
```

## webpack loader

使用多个`loader`时`webpack`从右向左读

将css和图片当成模块打包到main.js供你的html做引用

官网找寻loader

![1573262603344](C:\Users\888\AppData\Roaming\Typora\typora-user-images\1573262603344.png)

### css loader

安装

```js
npm install --save-dev css-loader
```

用法

`css-loader` 解释(interpret) `@import` 和 `url()` ，会 `import/require()` 后再解析(resolve)它们。

**file.js**

```js
import css from 'file.css';
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      }
    ]
  }
}
```

### style loader

安装

```bash
npm install style-loader --save-dev
```

[用法](https://webpack.js.org/concepts/loaders)

建议将 `style-loader` 与 [`css-loader`](https://github.com/webpack/css-loader) 结合使用

**component.js**

```js
import style from './file.css'
```

**webpack.config.js**

```js
{
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          { loader: "style-loader" },
          { loader: "css-loader" }
        ]
      }
    ]
  }
}
```

### url loader

安装

```bash
npm install --save-dev url-loader
```

[用法](https://webpack.js.org/concepts/loaders)

`url-loader` 功能类似于 [`file-loader`](https://github.com/webpack-contrib/file-loader)，但是在文件大小（单位 byte）低于指定的限制时，可以返回一个 DataURL。

```js
import img from './image.png'
```

**webpack.config.js**

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|gif)$/,
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
}
```

加载图片的时候想在url前面加上一个特定的路径  `publicPath: 'dist/'`后期删掉

```js
module.exports = {
    entry: './src/main.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js',
        publicPath: 'dist/'
    }
}
```

图片的命名 `name: 'img/[name].[hash:8].[ext]'` 固定格式

```js
{
    test: /\.(png|jpg|gif|gpeg)$/,
        use: [
            {
                loader: 'url-loader',
                options: {
                    limit: 8192,
                    name: 'img/[name].[hash:8].[ext]'
                }
            }
        ]
}

```

## 将ES6转化为ES5

```js
npm install --save-dev babel-loader@7 babel-core babel-preset-es2015
```

```js
{
    test: /\.js$/,
        exclude: /(node_modules|bower_components)/,
            use: {
                loader: 'babel-loader',
                options: {
                    presets: ['es2015']
                }
            }
}
```

## Vue

安装

```js
npm install vue --save/-S
```

导入

```js
import Vue from 'vue'
```

如果想运行起来的话需要做得操作 `resolve`

```js
module.exports = {
  entry: './src/main.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
    publicPath: 'dist/'
  },
  module: {
    rules: []
  },
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js'
    }
  }
}
```

在使用.vue文件的时候 webpack默认不知道如何加载，需要用loader

```js
npm install vue-loader vue-template-compiler --save-dev
```

修改`webpack.config.js`文件

## plugin

#### `HtmlWebpackPlugin`插件

```js
npm install html-webpack-plugin --save-dev
new HtmlWebpackPlugin({
    template: 'index.html'
})
```

`js压缩`插件

```js
npm install uglifyjs-webpack-plugin@1.1.1 --save-dev
new uglifyJsPlugin()


module.exports = {
  entry: './src/main.js',
  output: {
    
  },
  plugins: [
    new webpack.BannerPlugin('This is belong to delay_boy'),
    new HtmlWebpackPlugin({
      template: 'index.html'
    }),
    new uglifyJs()
  ]
}
```

## 搭建本地服务器

安装

```js
npm install --save-dev webpack-dev-server@2.9.1
devServer: {
    contentBase: './dist',
    inline: true
}
在package.json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack",
    "dev": "webpack-dev-server --open"
  },
```

