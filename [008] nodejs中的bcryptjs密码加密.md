## bcryptjs密码加密
bcryptjs一个第三方密码加密库，是对原有bcrypt的优化，优点是不需要安装任何依赖

[npmjs地址](https://www.npmjs.com/package/bcryptjs)

## 引入bcryptjs库

```
npm install bcryptjs
```

```
var bcrypt = require('bcryptjs');
...
```

### 同步用法(Sync)
生成hash密码
```
var bcrypt = require('bcryptjs');
var salt = bcrypt.genSaltSync(10);
var hash = bcrypt.hashSync("B4c0/\/", salt);
// Store hash in your password DB. 
```

密码验证
```
// Load hash from your password DB. 
bcrypt.compareSync("B4c0/\/", hash); // true 
bcrypt.compareSync("not_bacon", hash); // false 
```

快速生成hash值
```
var hash = bcrypt.hashSync('bacon', 8);
```

### 异步用法(Async)
生成hash密码
```
var bcrypt = require('bcryptjs');
bcrypt.genSalt(10, function(err, salt) {
    bcrypt.hash("B4c0/\/", salt, function(err, hash) {
        // Store hash in your password DB. 
    });
});
```
密码验证
```
// Load hash from your password DB. 
bcrypt.compare("B4c0/\/", hash, function(err, res) {
    // res === true 
});
bcrypt.compare("not_bacon", hash, function(err, res) {
    // res === false 
});
 
// As of bcryptjs 2.4.0, compare returns a promise if callback is omitted: 
bcrypt.compare("B4c0/\/", hash).then((res) => {
    // res === true 
});
```
快速生成hash值
```
bcrypt.hash('bacon', 8, function(err, hash) {
});
```

