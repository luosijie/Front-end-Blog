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
