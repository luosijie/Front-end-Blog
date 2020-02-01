> 目前接触了一些室内地图的开发工作，二维的、三维的，数据源基本都是采用geojson格式

基于geojson的地图绘制目前已经有比较成熟的框架和解决方案了。

但是今天我们还是要在Threejs里来简单实现一下三维数据的展示。


![](https://user-gold-cdn.xitu.io/2020/2/1/16fffcf9d4bac4e8?w=1330&h=1278&f=png&s=321584)
<p align="center">
<a href="https://github.com/luosijie/threejs-examples/tree/master/src/pages/china-map">代码地址</a>
<a href="https://luosijie.github.io/threejs-examples/#/china-map">预览地址</a>
</p>

主要实现了2个功能
+ 三维地图展示
+ POI信息显示

## 数据采集
首先我们需要一份[中国省份的轮廓数据](http://datav.aliyun.com/tools/atlas/#&lat=0.7031073524364909&lng=172.96875&zoom=2)

![geojson代码截图](https://user-gold-cdn.xitu.io/2020/2/1/16ffef0fcdfa2b49?w=768&h=1162&f=png&s=96104)

在这份数据我们需要的字段有
+ ##### properties.name 
  用于POI信息的展示
+ ##### properties.centroid
  用于POI信息的定位
+ ##### geometry.coordinates
  用于构建我们的三维模型

## 三维环境搭建

![](https://user-gold-cdn.xitu.io/2020/2/1/16fff9a4365abaed?w=1904&h=1262&f=png&s=176604)
#### 注意
+ 以下仅是核心代码
+ 绿色的线是y轴
+ 红色的线是x轴
+ 蓝色的线是z轴
```
    mounted () {
        // 初始化3D环境
        this.initEnvironment()
        // 构建光照系统
        this.buildLightSystem()
        // 构建辅助系统
        this.buildAuxSystem()
    },
    methods: {
        // 初始化3D环境
        initEnvironment () {
            this.scene = new THREE.Scene();
            this.scene.background = new THREE.Color(0xf0f0f0)
            // 建一个空对象存放对象
            this.map = new THREE.Object3D()
            // 设置相机参数
            this.setCamera();
            // 初始化
            this.renderer = new THREE.WebGLRenderer({
                alpha: true,
                canvas: document.querySelector('canvas')
            })
            this.renderer.setPixelRatio(window.devicePixelRatio)
            this.renderer.setSize(window.innerWidth, window.innerHeight - 10)
            document.addEventListener('mousemove', this.onDocumentMouseMove, false)
            window.addEventListener('resize', this.onWindowResize, false)
        },
        setCamera () {
            this.camera = new THREE.PerspectiveCamera(35, window.innerWidth / window.innerHeight, 1, 10000);
            this.camera.position.set(0, -70, 90);
            this.camera.lookAt(0, 0, 0);
        },
        // 构建辅助系统: 网格和坐标
        buildAuxSystem () {
            let axisHelper = new THREE.AxesHelper(2000)
            this.scene.add(axisHelper)
            let gridHelper = new THREE.GridHelper(600, 60)
            this.scene.add(gridHelper)
            let controls = new THREE.OrbitControls(this.camera, this.renderer.domElement)
            controls.enableDamping = true
            controls.dampingFactor = 0.25
            controls.rotateSpeed = 0.35
        },
        // 光照系统
        buildLightSystem () {
            let directionalLight = new THREE.DirectionalLight(0xffffff, 1.1);
            directionalLight.position.set(300, 1000, 500);
            directionalLight.target.position.set(0, 0, 0);
            directionalLight.castShadow = true;

            let d = 300;
            const fov = 45 //拍摄距离  视野角值越大，场景中的物体越小
            const near = 1 //相机离视体积最近的距离
            const far = 1000//相机离视体积最远的距离
            const aspect = window.innerWidth / window.innerHeight; //纵横比
            directionalLight.shadow.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
            directionalLight.shadow.bias = 0.0001;
            directionalLight.shadow.mapSize.width = directionalLight.shadow.mapSize.height = 1024;
            this.scene.add(directionalLight)

            let light = new THREE.AmbientLight(0xffffff, 0.6)
            this.scene.add(light)

        },
        // 根据浏览器窗口变化动态更新尺寸
        onWindowResize () {
            this.camera.aspect = window.innerWidth / window.innerHeight;
            this.camera.updateProjectionMatrix();
            this.renderer.setSize(window.innerWidth, window.innerHeight);
        },
        onDocumentMouseMove (event) {
            event.preventDefault();
        }
    }
```
## 绘制地图模型
### 数据分析
接下来我们需要根据 geometry.coordinates 来绘制地图
```
            "geometry": {
                "type": "MultiPolygon",
                "coordinates": [
                    [
                        [
                            [
                                117.210024,
                                40.082262
                            ],
                            [
                                117.105315,
                                40.074479
                            ],
                            [
                                117.105315,
                                40.074479
                            ],
                            ...
                        ]
                    ]
                ]
            }

```
#### 坐标转化
我们的坐标数据是经纬度坐标，我们需要把它转化成平面坐标
这里用到了 [d3-geo](https://www.npmjs.com/package/d3-geo) 的坐标转化方法

#### 多面绘制
注意这里的类型是 MultiPolygon（多面），我们的坐标点是嵌套在多层数组里面的。

因为我们的数据中，

有的省份轮廓是闭合的
![](https://user-gold-cdn.xitu.io/2020/2/1/16fffacdda9b67cc?w=756&h=732&f=png&s=41131)

有的省份是多个部分组成的

![](https://user-gold-cdn.xitu.io/2020/2/1/16fffae49acf70ab?w=642&h=732&f=png&s=42692)

### 代码实现

我们的模型分成2部分
1. 主体部分：我们用THREE.Shape() + THREE.ExtrudeGeometry()来实现
2. 轮廓线部分：我们用THREE.Line()来实现
```
        initMap () {
            // d3-geo转化坐标
            const projection = d3geo.geoMercator().center([104.0, 37.5]).scale(80).translate([0, 0]);
            // 遍历省份构建模型
            chinaJson.features.forEach(elem => {
                // 新建一个省份容器：用来存放省份对应的模型和轮廓线
                const province = new THREE.Object3D()
                const coordinates = elem.geometry.coordinates
                coordinates.forEach(multiPolygon => {
                    multiPolygon.forEach(polygon => {
                        // 这里的坐标要做2次使用：1次用来构建模型，1次用来构建轮廓线
                        const shape = new THREE.Shape()
                        const lineMaterial = new THREE.LineBasicMaterial({ color: 0xffffff })
                        const linGeometry = new THREE.Geometry()
                        for (let i = 0; i < polygon.length; i++) {
                            const [x, y] = projection(polygon[i])
                            if (i === 0) {
                                shape.moveTo(x, -y)
                            }
                            shape.lineTo(x, -y);
                            linGeometry.vertices.push(new THREE.Vector3(x, -y, 4.01))
                        }
                        const extrudeSettings = {
                            depth: 4,
                            bevelEnabled: false
                        };
                        const geometry = new THREE.ExtrudeGeometry(shape, extrudeSettings)
                        const material = new THREE.MeshBasicMaterial({ color: '#d13a34', transparent: true, opacity: 0.6 })
                        const mesh = new THREE.Mesh(geometry, material)
                        const line = new THREE.Line(linGeometry, lineMaterial)
                        province.add(mesh)
                        province.add(line)
                    })
                })
                // 将geojson的properties放到模型中，后面会用到
                province.properties = elem.properties
                if (elem.properties.centroid) {
                    const [x, y] = projection(elem.properties.centroid)
                    province.properties._centroid = [x, y]
                }
                this.map.add(province)
            })
            this.scene.add(this.map)
        }
```

实现后的效果是这样
![](https://user-gold-cdn.xitu.io/2020/2/1/16fffbe668e686f5?w=1026&h=878&f=png&s=96505)

## POI信息显示
如果是在室内地图的开发，我们通常会需要显示模块的一些信息，比如名称、图标之类的。这里我们就简单显示一下省份的名称就好。

我的做法是:
1. ### 获取每个省份模块的中心点坐标，并转化成屏幕坐标
2. ### 新建一个canvas，将省份名称根据坐标绘制到canvas上
3. ### 解决坐标的碰撞问题

### 代码实现

```
        showName () {
            const width = window.innerWidth
            const height = window.innerHeight
            let canvas = document.querySelector('#name')
            if (!canvas) return
            canvas.width = width;
            canvas.height = height;
            const ctx = canvas.getContext('2d');
            // 新建一个离屏canvas
            const offCanvas = document.createElement('canvas')
            offCanvas.width = width
            offCanvas.height = height
            const ctxOffCanvas = canvas.getContext('2d');
            // 设置canvas字体样式
            ctxOffCanvas.font = '16.5px Arial';
            ctxOffCanvas.strokeStyle = '#FFFFFF';
            ctxOffCanvas.fillStyle = '#000000';
            // texts用来存储显示的名称，重叠的部分就不会放到里面
            const texts = [];
            /**
             * 遍历省份数据，有2个核心功能
             * 1. 将3维坐标转化成2维坐标
             * 2. 后面遍历到的数据，要和前面的数据做碰撞对比，重叠的就不绘制
             * */
            this.map.children.forEach((elem, index) => {
                if (!elem.properties._centroid) return
                // 找到中心点
                const y = -elem.properties._centroid[1]
                const x = elem.properties._centroid[0]
                const z = 4
                // 转化为二维坐标
                const vector = new THREE.Vector3(x, y, z)
                const position = vector.project(this.camera)
                // 构建文本的基本属性：名称，left, top, width, height -> 碰撞对比需要这些坐标数据
                const name = elem.properties.name
                const left = (vector.x + 1) / 2 * width
                const top = -(vector.y - 1) / 2 * height
                const text = {
                    name,
                    left,
                    top,
                    width: ctxOffCanvas.measureText(name).width,
                    height: 16.5
                }
                // 碰撞对比
                let show = true
                for (let i = 0; i < texts.length; i++) {
                    if (
                        (text.left + text.width) < texts[i].left ||
                        (text.top + text.height) < texts[i].top ||
                        (texts[i].left + texts[i].width) < text.left ||
                        (texts[i].top + texts[i].height) < text.top
                    ) {
                        show = true
                    } else {
                        show = false
                        break
                    }
                }
                if (show) {
                    texts.push(text)
                    ctxOffCanvas.strokeText(name, left, top)
                    ctxOffCanvas.fillText(name, left, top)
                }
            })
            // 离屏canvas绘制到canvas中
            ctx.drawImage(offCanvas, 0, 0)
        }
```
> 注意，因为我们的canvas是叠在threejs的canvas上仅作为展示的，所以需要加个样式 pointer-events: none;

![](https://user-gold-cdn.xitu.io/2020/2/1/16fffcf10c836c56?w=1602&h=1290&f=png&s=192346)

> 谢谢阅读
