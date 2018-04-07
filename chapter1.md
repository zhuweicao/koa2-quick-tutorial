# 快速体验

### 安装 koa2

```shell
// 初始化 package.json
npm init

// 安装 koa2
npm install koa --install
```

### Hello World 代码

```js
// 引入 koa 模块
const Koa = require('koa')
// 创建 koa 应用
const app = new Koa()

// 处理请求
app.use( async ctx => {
    ctx.body = "Hello World"
})

// 启动应用
app.listen(3000)
console.log("starting at port 3000")
```



