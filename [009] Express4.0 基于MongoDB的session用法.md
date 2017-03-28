# Express4.0 基于MongoDB的session用法
Session —— 用来存储特定用户会话所需的属性及配置信息。
### 安装依赖
Express4.0将session模块拆分出来
```
npm install express-session
```
```
npm install connect-mongo
```

### 引入依赖
```
var express = require('express');
var cookieParser = require('cookie-parser');
var session = require('express-session');
var mongoStore = require('connect-mongo')(session);
```

### 配置Session
```
app.use(session({
  secret: 'luo',
  saveUninitialized: true,
  resave: false,
  cookie: {maxAge: 80000},
  store: new mongoStore({
    url: dbUrl
  })
}))
```

### 赋值
```
req.session.username = 'luo'
res.end('ok')//session在res.end()之后才会成功保存
```
