## 路径起别名

1.新建 vue.config.js 文件，和package.json同层级

```js
const path = require('path');

function resolve(dir) {
  return path.join(__dirname, dir);
}
module.exports = {
  lintOnSave: true,
  chainWebpack: (config) => {
    config.resolve.alias
      .set('@', resolve('src'))
      // 需要重启 IDE
      .set('styles',resolve('src/assets/styles'))
      // 这里只写了两个个，你可以自己再加，按这种格式.set('', resolve(''))
  }
};
```

2. 设置别名

```js
// 按这种格式.set('', resolve(''))
.set('styles',resolve('src/assets/styles'))
```

2. 重启IDE
   一定要重启IDE，否则会报错！！！

4. 使用

```js
//@：代表src目录，在css中引入其他css，使用 @ 时，前面需要加上 ~
@import '~@/assets/stylesstyles/variable.styl'
```

5. 设置别名后写法：

```js
// styles就是我在vue.config.js中设置的路径别名，代表 'src/assets/styles' 路径 
@import '~styles/variable.styl'
```

原文链接：https://blog.csdn.net/panchang199266/article/details/90145638