前段时间朋友介绍了一个很帅的网站 [http://www.species-in-pieces.com/](http://www.species-in-pieces.com/) <br>
F12看了一下 Dom 结构，发现作者只是结合了 CSS3 的 clip-path 和 transition 特性，就实现了很厉害的效果，每个转场和动物的细节都做得很棒。 <br>

然后我根据自己的理解做了一个Demo, 并将核心js部分打包了出来，欢迎感兴趣的朋友一起交流

![Demo for ani-clipath](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-demo.gif?raw=true)

> Demo中的图片来自 [https://www.behance.net/tomanders](https://www.behance.net/tomanders), 我只是做了低边处理

#### 预览地址: [https://luosijie.github.io/ani-clipath/](https://luosijie.github.io/ani-clipath/)
#### 源码地址: [https://github.com/luosijie/ani-clipath](https://github.com/luosijie/ani-clipath)

## 实现过程

先从一个简单的 clip-path 变形开始

![clip-path 动画](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-simple.gif?raw=true)

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>simple move</title>
  <style>
    .polygon{
      width: 600px;
      height: 300px;
      background-color: black;
      clip-path: polygon(20% 30%, 0 70%, 40% 70%);
      animation: move 1s infinite alternate;
    }
    @keyframes move {
      to {
        background-color: grey;
        clip-path: polygon(80% 70%, 100% 30%, 60% 30%);
      }
    }
  </style>
</head>
<body>
 <div class="polygon"></div>
</body>
</html>
```

这就是Demo中所需要的基本动画 **变形** + **变色**, 至于如何构建精美的图形变化, 就需要一点想象力和设计能力了

### 设计
我们需要先设计出满意的低边图案，注意控制三角形的数量，越少越好越少越好越少越好越少越好越少越好越少越好

![shark](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-shark.png?raw=true)

![shark](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-bird.png?raw=true)

![shark](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-fee.png?raw=true)

接下来，需要将每个三角形的坐标和色值转化为数据格式。我目前找不到很高效的转化方式，就用标注软件标注完后，手工录入,最后画了3个就结束了。所以三角形的数量越少越好越少越好越少越好越少越好越少越好越少越好。

> 或者你有很好的方式介绍，请一定要告诉我。

![图形转为数据](https://github.com/luosijie/Front-end-Blog/blob/master/img/ani-clipath-dada.png?raw=true)

现在准备工作完成了，最痛苦的阶段也已经过去了

### 码起

代码实现的基本思路是这样的

1. 每个动物都是由 **36** 个不同颜色的三角形拼接而成, 所以需要 **36** 个 div 来显示
2. 每一次变化都将 36 个div 的 clip-path 属性重新赋值
3. 每个 div 都设置 transition 属性, 让转场自动实现

区区几行代码就不贴出来了，感兴趣的移步 [github](https://github.com/luosijie/ani-clipath)

> 欢迎star
