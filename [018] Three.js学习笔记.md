### 入门Demo
```js
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  <title>JS Bin</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r82/three.min.js"></script>
</head>
<body>
  <div id="webgl-output"></div>
  <script>
    // 构建场景
    var scene = new THREE.Scene();
    // 构建摄像机
    var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
    // 构建渲染器
    var renderer = new THREE.WebGLRenderer();
    
    // 渲染器设置
    renderer.setClearColor(0xEEEEEE);
    renderer.setSize(window.innerWidth, window.innerHeight);
    
    // 摄像机设置
    camera.position.x = -30;
    camera.position.y = 40;
    camera.position.z = 30;
    camera.lookAt(scene.position)
    
    // 添加辅助坐标
    var axes = new THREE.AxisHelper(20);
    scene.add(axes)
    
    // 添加平面对象
    var planeGeometry = new THREE.PlaneGeometry(60,20,1,1);
    var planeMaterial = new THREE.MeshLambertMaterial({color: 0xcccccc});
    var plane = new THREE.Mesh(planeGeometry, planeMaterial);

    plane.rotation.x = -0.5*Math.PI
    plane.position.x = 15;
    plane.position.y = 0;
    plane.position.z = 0;

    scene.add(plane)
    
    // 添加正方体对象
    var cubeGeometry = new THREE.CubeGeometry(4,4,4)
    var cubeMaterial = new THREE.MeshLambertMaterial({color: 0xff0000, wireframe: true});
    var cube = new THREE.Mesh(cubeGeometry,cubeMaterial)

    cube.position.x = -4;
    cube.position.y = 3;
    cube.position.z = 0;

    scene.add(cube)
    
    // 添加球体对象
    var sphereGeometry = new THREE.SphereGeometry(4,20,20);
    var sphereMaterial = new THREE.MeshLambertMaterial({color: 0x7777ff, wireframe: true});
    var sphere = new THREE.Mesh(sphereGeometry, sphereMaterial);

    sphere.position.x = 20;
    sphere.position.y = 4;
    sphere.position.z = 2;

    scene.add(sphere)
    
    // 添加放射性光源
    var spotLight = new THREE.SpotLight(0xffffff)
    spotLight.position.set(-40, 60, -10)

    scene.add(spotLight)
    
    // 将渲染结果加载到Dom容器
    var webglOutput = document.querySelector('#webgl-output')
    webglOutput.appendChild(renderer.domElement)
    
    // 渲染器开始渲染
    renderer.render(scene, camera)
  </script>
</body>
</html>
```
# 构建几何体
## 二维几何体
### 平面 PlaneGeomery
```js
var geometry = new THREE.PlaneGeometry( 5, 20, 32 );
var material = new THREE.MeshBasicMaterial( {color: 0xffff00, side: THREE.DoubleSide} );
var plane = new THREE.Mesh( geometry, material );
scene.add( plane );
```
| 属性           | 必须  | 描述               |
| :------------- | :--- | :----------------- |
| width          | 是   | 指定平面的宽度      |
| height         | 是   | 指定平面高度        |
| widthSegments  | 否   | 指定平面宽度划分段数 |
| heightSegments | 否   | 指定平面高度划分段数 |

### 圆形 CircleGeomery
```js
var geometry = new THREE.CircleGeometry( 5, 32 );
var material = new THREE.MeshBasicMaterial( { color: 0xffff00 } );
var circle = new THREE.Mesh( geometry, material );
scene.add( circle );
```
| 属性           | 必须  | 描述               |
| :------------- | :--- | :----------------- |
| radius          | 是   | 指定圆形的半径     |
| segmentals      | 否   | 指定圆形的分段        |
| thetaStart  | 否   | 指定圆形的开始,取值范围是 0 到 2 * PI |
| thetaLength | 否   | 指定圆形画多大,默认 2 * PI |

### 自定义图形 ShapeGeomery
```js
var x = 0, y = 0;

var heartShape = new THREE.Shape();

heartShape.moveTo( x + 5, y + 5 );
heartShape.bezierCurveTo( x + 5, y + 5, x + 4, y, x, y );
heartShape.bezierCurveTo( x - 6, y, x - 6, y + 7,x - 6, y + 7 );
heartShape.bezierCurveTo( x - 6, y + 11, x - 3, y + 15.4, x + 5, y + 19 );
heartShape.bezierCurveTo( x + 12, y + 15.4, x + 16, y + 11, x + 16, y + 7 );
heartShape.bezierCurveTo( x + 16, y + 7, x + 16, y, x + 10, y );
heartShape.bezierCurveTo( x + 7, y, x + 5, y + 5, x + 5, y + 5 );

var geometry = new THREE.ShapeGeometry( heartShape );
var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
var mesh = new THREE.Mesh( geometry, material ) ;
scene.add( mesh );
```
| 属性           | 必须  | 描述               |
| :------------- | :--- | :----------------- |
| shape          | 是   | 指定自定义图形形状     |
| curveSegments      | 否   | 指定自定义图形分段        |

## 三维几何体
### 立方体 BoxGeometry
```js
var geometry = new THREE.BoxGeometry( 1, 1, 1 );
var material = new THREE.MeshBasicMaterial( {color: 0x00ff00} );
var cube = new THREE.Mesh( geometry, material );
scene.add( cube );
```
| 属性           | 必须  | 描述       |
| :------------- | :--- | :--------- |
| width          | 是   | 指定宽度    |
| height         | 是   | 指定高度    |
| depth          | 是   | 指定深度    |
| widthSegments  | 否   | 指定宽度分段 |
| heightSegments | 否   | 指定高度分段 |
| depthSegments  | 否   | 指定深度分段 |

### 球体 SphereGeometry
```js
var geometry = new THREE.SphereGeometry( 5, 32, 32 );
var material = new THREE.MeshBasicMaterial( {color: 0xffff00} );
var sphere = new THREE.Mesh( geometry, material );
scene.add( sphere );
```
| 属性           | 必须  | 描述       |
| :------------- | :--- | :--------- |
| radius          | 否   | 指定球体半径,默认 50    |
| widthSegments         | 是   | 指定球体竖直方向上的分段数,默认 8    |
| heightSegments | 否   | 指定球体水平方向上的分段数,默认6    |
| phiStart  | 否   | 指定 x 轴绘制起点,取值范围 0 到 2 * PI |
| phiLength | 否   | 指定从 phiStart 开始绘制的长度 |
| thetaStart  | 否   | 指定 y 轴绘制起点,取值范围 0 到 2 * PI |
| thetaLength  | 否   | 指定从 thetaStar开始绘制的长度 |

### 圆柱体 CylinderGeometry
```js
var geometry = new THREE.CylinderGeometry( 5, 5, 20, 32 );
var material = new THREE.MeshBasicMaterial( {color: 0xffff00} );
var cylinder = new THREE.Mesh( geometry, material );
scene.add( cylinder );
```
| 属性           | 必须  | 描述       |
| :------------- | :--- | :--------- |
| radius          | 否   | 指定球体半径,默认 50    |
| widthSegments         | 是   | 指定球体竖直方向上的分段数,默认 8    |
| heightSegments | 否   | 指定球体水平方向上的分段数,默认6    |
| phiStart  | 否   | 指定 x 轴绘制起点,取值范围 0 到 2 * PI |
| phiLength | 否   | 指定从 phiStart 开始绘制的长度 |
| thetaStart  | 否   | 指定 y 轴绘制起点,取值范围 0 到 2 * PI |
| thetaLength  | 否   | 指定从 thetaStar开始绘制的长度 |
