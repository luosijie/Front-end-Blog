最近在离职的空窗期，感觉大把的时间不能用来浪费，就试着仿照微信跳一跳写一个极简版的
##### 那么到底是简单到什么程度，差不多就是到这个程度吧
![图片]()
#### 预览地址: [https://luosijie.github.io/threejs-examples/jump.html](https://luosijie.github.io/threejs-examples/jump.html)
#### 源码地址: [https://github.com/luosijie/threejs-examples/blob/master/jump.html](https://github.com/luosijie/threejs-examples/blob/master/jump.html)
> 由于是第一次尝试写游戏, 也不知道套路对不对, 大家看着玩就好, 手机上的效果不太行

#### 下面是实现过程
### 游戏分析
首先分析一下一个这样的游戏需要什么元素
![游戏元素] ()
1. Three.js必备元素: 场景，灯光，摄像机
2. 一块又一块的方块
3. **会跳的那个**，或者叫游戏者
4. 以上

### 游戏过程
1. 初始一个场景, 场景中有一个 **会跳的那个** 和 2个方块
2. 鼠标按下储存 能量值
3. 鼠标放开, **会跳的那个** 根据 能量值 和 第2个方块的方向 跳出去
4. **会跳的那个** 落到方块的上平面时, 根据 位置 判断这一跳是成功还是失败
5. 成功后进入下一步，失败就根据 位置 执行不同的摔倒方式

#### 关于游戏的碰撞, 我们要考虑这几种情况

### 代码结构
> 感兴趣的麻烦移步 [github](https://github.com/luosijie/threejs-examples/blob/master/jump.html)

```js
var Game = function () {
  ...
}
Game.prototype = {
  init:  // 初始化
  restart: // 重新开始
  addSuccessFn:  // 成功进入下一步，执行外部函数, 用于更新分数
  addFailedFn: // 游戏失败, 执行外部函数, 用于显示失败弹窗
  _createJumper: // 创建 会跳的那个
  _createCube: // 创建方块
  _setLight: // Three.js设置光照
  _setCamera: // Three.js设置相机
  _setRenderer: // Three.js设置渲染器
  _render: // Three.js 执行渲染
  _createHelpers: // Three.js场景辅助工具
  _checkUserAgent: // 检测是否是移动端
  _handleWindowResize: // 窗口缩放绑定函数
  _handleMousedown: // 鼠标按下绑定函数
  _handleMouseup: // 鼠标松开绑定函数
  _fallingRotate: // 会跳的那个 摔落动画
  _falling: // 会跳的那个 摔落
  _checkInCube: // 判断落点位置
  _updateCameraPos: // 更新相机坐标参数
  _updateCamera: // 更新相机
  _setSize:   // 设置画布尺寸
}

```
调用
```js
var game = new Game(success,failed)
game.init()
game.addSuccessFn(success)
game.addFailedFn(failed)

...

// 游戏重新开始，执行函数
function restart () {
	...
}
// 游戏失败执行函数
function failed(){
	...
}
// 游戏成功，更新分数
function success (score) {
	...
}
```

