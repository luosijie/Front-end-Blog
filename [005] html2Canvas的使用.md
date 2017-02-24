# html2Canvas的使用
html2Canvas是一款可以将html Dom 转化为 canvas图片 的js插件         
gitHub地址: https://github.com/niklasvh/html2canvas

### 浏览器支持

+ Firefox 3.5+
+ Google Chrome
+ Opera 12+
+ IE9+
+ Safari 6+

### 局限
html2Canvas解析的图片必须符合同源策略，不然会解析失败


### 基本用法

```
html2canvas(element, options)
```

其中       
element 是需要渲染的 Dom对象     
opsitons 是可选参数

可以使用 onrandered 回调函数渲染 canvas：

```
html2Canvas(element, {
  onrendered: function(canvas){
    //canva就是经过解析后生成的canvse图片
  }
})
```
### 可选参数
|参数名称          |类型      |   默认值   |描述                               |
| ------          | ----    | ------    | ------                            |
| allowTaint      | boolean | false     | 允许跨域                           |
| background      | string  | #fff      | canvas的背景颜色,如果没有设定默认透明 |
| height          |	number  |	null      |	canvas高度设定                     |
| letterRendering |	boolean |	false     |	是否设置字间距                      |
| logging         |	boolean |	false     |	是否在console.log()中输出信息       |
| proxy           |	string  |	undefined |	设置代理地址                        |
| taintTest       |	boolean |	true      |	是否在渲染前测试图片                 |
| timeout         |	number  |	0         |	图片加载延迟，默认延迟为0,单位毫秒     |
| width           |	number  |	null      |	canvas宽度                         |
| useCORS         |	boolean |	false     |	是否在恢复代理之前,尝试加载跨域图片    |
