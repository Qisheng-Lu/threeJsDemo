<template>
  <div>
    <div id="three-box"></div>
  </div>
</template>

<script>
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"
  import '@/assets/js/threeBSP';
  import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader'

  export default {
    name: 'Demo11',
    data() {
      return {
        scene: null,
        geometry: null,
        material: null,
        mesh: null,
        point: null,
        ambient: null,
        width: null,
        height: null,
        camera: null,
        renderer: null,
        wall1: null,
        imgUrl: null,
        rightDoor: null,
        leftDoor: null,
        window: null,
        duck: null,
        grass: null,
        road: null,
        road2: null
      }
    },
    created() {
      /**
       * 创建场景对象Scene
       */
      this.scene = new THREE.Scene();


      var gltfLoader = new GLTFLoader()
      gltfLoader.load(
        `/static/3d/warehouse/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.05,0.05,0.05);//网格模型缩放
          gltf.scene.position.set(0, 0, 0) //定位
          gltf.scene.rotation.y = Math.PI //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )




      /**
       * 光源设置
       */
      //点光源
      this.point = new THREE.PointLight(0x999999);
      this.point.position.set(400, 200, 300); //点光源位置
      this.scene.add(this.point); //点光源添加到场景中

      //环境光
      this.ambient = new THREE.AmbientLight(0x999999);
      this.scene.add(this.ambient);

      /**
       * 相机设置
       */
      this.width = window.innerWidth; //窗口宽度
      this.height = window.innerHeight; //窗口高度
      var k = this.width / this.height; //窗口宽高比
      var s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
      //创建相机对象
      //this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 0.1, 1000);
      this.camera = new THREE.PerspectiveCamera( 80, window.innerWidth/window.innerHeight, 0.1, 1000);
      this.camera.position.set(500, 400, 460); //设置相机位置
      // this.camera.lookAt(this.scene.position); //设置相机方向(指向的场景对象)
      this.camera.lookAt({x:0,y:0,z:0}); //设置相机方向(指向的场景对象)



      /**
       * 创建渲染器对象
       */
      this.renderer = new THREE.WebGLRenderer({
        //增加以下两个属性来抗锯齿
        antialias:true,
        alpha:true
      });
      this.renderer.setSize(this.width, this.height);//设置渲染区域尺寸
      this.renderer.setClearColor(0x0c5460, 1); //设置背景颜色
      document.body.appendChild(this.renderer.domElement); //body元素中插入canvas对象
      //执行渲染操作   指定场景、相机作为参数

      var axisHelper = new THREE.AxisHelper(400);
      this.scene.add(axisHelper);


      this.renderA()
      setTimeout(this.renderA,200)
      var controls = new THREE.OrbitControls(this.camera,this.renderer.domElement);//创建控件对象
      controls.addEventListener('change', this.renderA);//监听鼠标、键盘事件
    },
    methods: {
      renderA() {
        this.renderer.render(this.scene, this.camera);//执行渲染操作
        // requestAnimationFrame(this.renderA);
      },
      createWall(width,height,depth,material,positionX,positionY,positionZ){
        var wall = new THREE.BoxGeometry(width, height, depth); //创建一个立方体几何对象Geometry
        var mesh = new THREE.Mesh(wall, material); //网格模型对象Mesh
        mesh.position.set(positionX,positionY,positionZ);//设置mesh3模型对象的xyz坐标为120,0,0
        this.scene.add(mesh);
      },
      returnWallObject(width, height, depth, angle, material, x, y, z, name) {//挖孔墙
        var cubeGeometry = new THREE.BoxGeometry(width, height, depth);
        var cube = new THREE.Mesh(cubeGeometry, material);
        cube.position.x = x;
        cube.position.y = y;
        cube.position.z = z;
        cube.rotation.y += angle * Math.PI;
        cube.name = name;
        return cube;
      },
      createResultBsp(bsp,objects_cube){
        var material = new THREE.MeshPhongMaterial({color:0x9cb2d1,specular:0x9cb2d1,shininess:30,transparent:true,opacity:1});
        var BSP = new ThreeBSP(bsp);
        // console.log(BSP)
        for(var i = 0; i < objects_cube.length; i++){
          var less_bsp = new ThreeBSP(objects_cube[i]);
          BSP = BSP.subtract(less_bsp);
        }
        var result = BSP.toMesh(material);
        result.material.flatshading = THREE.FlatShading;
        result.geometry.computeFaceNormals();  //重新计算几何体侧面法向量
        result.geometry.computeVertexNormals();
        result.material.needsUpdate = true;  //更新纹理
        result.geometry.buffersNeedUpdate = true;
        result.geometry.uvsNeedUpdate = true;
        this.scene.add(result);
      },
      createDoor_left(width, height, depth, angle, x, y, z, name){//左边门
        var loader = new THREE.TextureLoader();
        var _this = this
        loader.load(this.leftDoor,function(texture){
          var doorgeometry = new THREE.BoxGeometry(width, height, depth);
          doorgeometry.translate(50, 0, 0);
          var doormaterial = new THREE.MeshBasicMaterial({map:texture,color:0xffffff});
          doormaterial.opacity = 1.0;
          doormaterial.transparent = true;
          var door = new THREE.Mesh( doorgeometry,doormaterial);
          door.position.set(x, y, z);
          door.rotation.y += angle*Math.PI;  //-逆时针旋转,+顺时针
          door.name = name;
          _this.scene.add(door);
        })
      },
      createDoor_right(width, height, depth, angle, x, y, z, name){//右边门
        var loader = new THREE.TextureLoader();
        var _this = this
        loader.load(this.rightDoor,function(texture){
          var doorgeometry = new THREE.BoxGeometry(width, height, depth);
          doorgeometry.translate(-50, 0, 0);
          var doormaterial = new THREE.MeshBasicMaterial({map:texture,color:0xffffff});
          doormaterial.opacity = 1.0;
          doormaterial.transparent = true;
          var door = new THREE.Mesh( doorgeometry,doormaterial);
          door.position.set(x, y, z);
          door.rotation.y += angle*Math.PI;  //-逆时针旋转,+顺时针
          door.name = name;
          _this.scene.add(door);
        });
      },
      createWindow(width, height, depth, angle, x, y, z, name) {//创建窗户
        var _this = this
        var loader = new THREE.TextureLoader();
        loader.load(this.window, function (texture) {
          var windowgeometry = new THREE.BoxGeometry(width, height, depth);
          var windowmaterial = new THREE.MeshBasicMaterial({map: texture, color: 0xffffff});
          windowmaterial.opacity = 1.0;
          windowmaterial.transparent = true;
          var window = new THREE.Mesh(windowgeometry, windowmaterial);
          window.position.set(x, y, z);
          window.rotation.y += angle * Math.PI;  //-逆时针旋转,+顺时针
          window.name = name;
          _this.scene.add(window);
        });
      },
      createGrass(width, height, depth, angle, x, y, z, name) {//创建窗户
        var windowgeometry = new THREE.BoxGeometry(width, height, depth);

// 加载树纹理贴图
        var texture = new THREE.TextureLoader().load(this.grass);
// 设置阵列
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
// uv两个方向纹理重复数量
        texture.repeat.set(10, 1);
        var material = new THREE.MeshLambertMaterial({
          map: texture,
        });
        var mesh = new THREE.Mesh(windowgeometry, material); //网格模型对象Mesh
        mesh.position.set(x, y, z);
        this.scene.add(mesh); //网格模型添加到场景中
        //mesh.rotateX(-Math.PI / 2);
      },
      createRoad(width, height, depth, angle, x, y, z, name) {
        var windowgeometry = new THREE.BoxGeometry(width, height, depth);
        var texture = new THREE.TextureLoader().load(this.road);
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
        texture.repeat.set(1, 10);
        texture.rotation = Math.PI/2;
// 设置纹理的旋转中心，默认(0,0)
        texture.center.set(0,0);
        var material = new THREE.MeshLambertMaterial({
          map: texture,
        });
        var mesh = new THREE.Mesh(windowgeometry, material); //网格模型对象Mesh
        mesh.position.set(x, y, z);
        this.scene.add(mesh);
      },
      createRoad2(width, height, depth, angle, x, y, z, name) {
        var windowgeometry = new THREE.BoxGeometry(width, height, depth);
        var texture = new THREE.TextureLoader().load(this.road2);
        texture.wrapS = THREE.RepeatWrapping;
        texture.wrapT = THREE.RepeatWrapping;
        texture.repeat.set(1, 10);
        var material = new THREE.MeshLambertMaterial({
          map: texture,
        });
        var mesh = new THREE.Mesh(windowgeometry, material); //网格模型对象Mesh
        mesh.position.set(x, y, z);
        this.scene.add(mesh);
      },
    }
  }
</script>

<style scoped>
  #three-box{
    width: 100%;
    height: 100%;
  }
</style>
