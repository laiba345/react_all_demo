# 第四章
## 理解
### 前置说明
1. React本身只关注界面，并不包含发送ajax请求的代码
2. 前端应用需要通过ajax请求与后台进行交互（json数据）
3. react应用中`需要集成第三方ajax库`（或自己封装）

### 常用的ajax请求库
1. Jquery；比较重，如果需要另外引入不建议使用
2. axios；轻量级，建议使用
    - 封装XmlHttpRequest对象的ajax
    - promise风格
    - 可以在浏览器端和node服务器端

### google中的插件FeHelper，用于整理数据

### 服务器同源策略
1. localhost:3000 => localhost:5000
2. 在react脚手架中通过配置代理来进行解决处理
    - 通过中间人来进行代理
    - 拦截的本质是因为ajax引擎来进行拦截；但是中间人是通过请求转发的形式来处理
    - 代理图
3. 解决
    - 方法1；通过在package.json文件下配置；并不是所有的信息都转化为给5000，
    只有3000没有的才转发给5000
        - 方法1弊端；只能配置一个代理，比较局限
        - 除了服务1还有服务2
    ```
    "proxy":"http://localhost:5000"
    ```
    - 方法2；
        - 需要在src中加上一个setupProxy.js文件
        - vue中该方法是在devserve中进行配置
        - 改了重要文件需要重启脚手架

### react脚手架配置代理总结
# react脚手架配置代理总结



## 方法一

> 在package.json中追加如下配置

```json
"proxy":"http://localhost:5000"
```

说明：

1. 优点：配置简单，前端请求资源时可以不加任何前缀。
2. 缺点：不能配置多个代理。
3. 工作方式：上述方式配置代理，当请求了3000不存在的资源时，那么该请求会转发给5000 （优先匹配前端资源）



## 方法二

1. 第一步：创建代理配置文件

   ```
   在src下创建配置文件：src/setupProxy.js
   ```

2. 编写setupProxy.js配置具体代理规则：

   ```js
   const proxy = require('http-proxy-middleware')
   
   module.exports = function(app) {
     app.use(
       proxy('/api1', {  //api1是需要转发的请求(所有带有/api1前缀的请求都会转发给5000)
         target: 'http://localhost:5000', //配置转发目标地址(能返回数据的服务器地址)
         changeOrigin: true, //控制服务器接收到的请求头中host字段的值
         /*
         	changeOrigin设置为true时，服务器收到的请求头中的host为：localhost:5000
         	changeOrigin设置为false时，服务器收到的请求头中的host为：localhost:3000
         	changeOrigin默认值为false，但我们一般将changeOrigin值设为true
         */
         pathRewrite: {'^/api1': ''} //去除请求前缀，保证交给后台服务器的是正常请求地址(必须配置)
       }),
       proxy('/api2', { 
         target: 'http://localhost:5001',
         changeOrigin: true,
         pathRewrite: {'^/api2': ''}
       })
     )
   }
   ```

说明：

1. 优点：可以配置多个代理，可以灵活的控制请求是否走代理。
2. 缺点：配置繁琐，前端请求资源时必须加前缀。

