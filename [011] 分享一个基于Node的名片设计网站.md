![home](https://github.com/luosijie/Front-end-Blog/blob/master/img/a.PNG?raw=true)

我现在本职工作是UI设计，但是在工作中也喜欢了解和学习前端技术，这是小网站是我去年开始学习Node之后开发的个人项目。这个项目边学边做，断断续续也有半年时间吧，期间不断增加新功能，优化用户体验。现在因为想构想新项目，不打算继续更新，所以分享给感兴趣的朋友，大家互相学习。

项目地址 [https://github.com/luosijie/card](https://github.com/luosijie/card)

预览地址 [http://card.luosj.me](http://card.luosj.me)

## 安装
1. 安装MongoDB并成功启动
2. git clone "https://github.com/luosijie/card"
3. npm install
4. npm start
5. 访问 localhost:3000

## 使用到的技术
1. Node + Express
2. MongoDB + Mongoose

## 实现功能

> 网站的基本功能是管理员编辑好名片模板并上传到服务器，用户通过简单编辑名片模板下载直接拿去打印店打印

1. 登录注册
2. 图片简单处理
3. 名片模板的上传和保存
4. 名片图片的打包和下载
4. 名片模板收藏
5. MongoDB数据增删改查

## 使用到的插件
1. html2Canvas 用于将html解析为图片
2. jsZip 用于图片打包
3. fileSaver 用于导出打包后的zip文件

## 网站的不足
1. 代码组织比较乱 ———— 哪天心情好再来整理
2. 没有兼容浏览器，目前只谷歌 火狐可以正常访问 ———— 人生苦短，我不兼容
3. 没有管理后台 ———— 这是一个比较大的问题

## 项目结构

![structure](https://github.com/luosijie/Front-end-Blog/blob/master/img/structure.PNG?raw=true)

## 项目部分截图
#### 首页
模板列表用hbs模板渲染

![structure](https://github.com/luosijie/Front-end-Blog/blob/master/img/home.PNG?raw=true)

#### 用户编辑页
看起来简单的页面结果花的时间最多

![structure](https://github.com/luosijie/Front-end-Blog/blob/master/img/edit.PNG)

#### 管理员上传模板
注意：这个页面的权限只有 **用户名为admin** 才可以访问，没办法就是这么不科学

![structure](https://github.com/luosijie/Front-end-Blog/blob/master/img/upload.PNG?raw=true)

#### 个人中心
我的收藏和基本账户设置功能

![personal](https://github.com/luosijie/Front-end-Blog/blob/master/img/personal.PNG?raw=true)

> 先这样了 欢迎大家star




