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
      let urlTemp = "assets/wall.jpg";
      let rightDoor = "assets/door_right.png";
      let leftDoor = "assets/door_left.png";
      let windowPic = "assets/window.png";
      let grass = "assets/grass2.jpg";
      let road = "assets/road3.jpg";
      let road2 = "assets/road2.jpg";
      //let duck = "assets/3d/bottom/scene.gltf";
      this.imgUrl = require("@/" + urlTemp)
      this.rightDoor = require("@/" + rightDoor)
      this.leftDoor = require("@/" + leftDoor)
      this.window = require("@/" + windowPic)
      //this.duck = require("@/" + duck)
      this.grass = require("@/" + grass)
      this.road = require("@/" + road)
      this.road2 = require("@/" + road2)
      //玻璃色： 0x87CEEB
      var canvas = document.createElement("canvas");
      var context = canvas.getContext('2d');
      canvas.width = 1800;
      canvas.height = 1200;
      context.save()
      context.fillStyle = 'white';
      context.fillRect(0, 0, context.canvas.width, context.canvas.height);
      context.lineWidth = 1;
      context.strokeStyle = '#ccc';
      for (var i = 40; i < context.canvas.width; i += 40) {
        context.beginPath();
        context.moveTo(i, 0);
        context.lineTo(i, context.canvas.height);
        context.closePath();
        context.stroke();
      }
      for (var j = 40; j < context.canvas.height; j += 40) {
        context.beginPath();
        context.moveTo(0, j);
        context.lineTo(context.canvas.width, j);
        context.closePath();
        context.stroke();
      }
      context.restore();
      /**
       * 创建场景对象Scene
       */
      this.scene = new THREE.Scene();
      /**
       * 创建网格模型
       */
      this.geometry = new THREE.BoxGeometry(400, 1, 300); //创建一个立方体几何对象Geometry
      var materiaGround = new THREE.MeshLambertMaterial({
        color: 0xffffff,
        transparent: true,
        opacity: 0.7,
        side: THREE.DoubleSide //两面可见
      }); //材质对象Material
      this.mesh = new THREE.Mesh(this.geometry, materiaGround); //网格模型对象Mesh
      this.mesh.position.set(0,-0.5,0);//设置mesh3模型对象的xyz坐标为120,0,0


      var texture = new THREE.CanvasTexture(canvas);
//打印纹理对象的image属性
// console.log(texture.image);
//矩形平面

      var material = new THREE.MeshPhongMaterial({
        map: texture, // 设置纹理贴图
      });
// 创建一个矩形平面网模型，Canvas画布作为矩形网格模型的纹理贴图
      var mesh = new THREE.Mesh(this.geometry, material);
      this.scene.add(this.mesh); //网格模型添加到场景中
      this.scene.add(mesh); //网格模型添加到场景中  //地面



      var wall1 = new THREE.BoxGeometry(1, 30, 300); //创建一个立方体几何对象Geometry
      var material2 = new THREE.MeshLambertMaterial({
        color: 0x87CEEB,
        wireframe: false,
        transparent: true,
        opacity: 0.7,
        side: THREE.DoubleSide //两面可见
      }); //材质对象Material
      var mesh1 = new THREE.Mesh(wall1, material2); //网格模型对象Mesh
      mesh1.position.set(-200,15,0);//设置mesh3模型对象的xyz坐标为120,0,0
      this.scene.add(mesh1); //网格模型添加到场景中



      var wall2 = new THREE.BoxGeometry(1, 30, 300); //创建一个立方体几何对象Geometry
      var mesh2 = new THREE.Mesh(wall2, material2); //网格模型对象Mesh
      mesh2.position.set(200,15,0);//设置mesh3模型对象的xyz坐标为120,0,0
      this.scene.add(mesh2); //网格模型添加到场景中




      var wall3 = new THREE.BoxGeometry(400, 30, 1); //创建一个立方体几何对象Geometry
      // var material3 = new THREE.MeshLambertMaterial({
      //   color: 0xC3C3C3,
      //   wireframe: false,
      //   transparent: true,
      //   opacity: 0.95,
      //   side: THREE.DoubleSide //两面可见
      // }); //材质对象Material
      // var mesh3 = new THREE.Mesh(wall3, material3); //网格模型对象Mesh
      // mesh3.position.set(0,15,-150);//设置mesh3模型对象的xyz坐标为120,0,0
      // this.scene.add(mesh3); //网格模型添加到场景中


      var matArrayB = new THREE.MeshLambertMaterial({
        color: 0xC3C3C3,
        wireframe: false,
        transparent: true,
        opacity: 1,
        side: THREE.DoubleSide //两面可见
      }); //材质对象Material

      var matArrayGood = new THREE.MeshLambertMaterial({
        color: 0x124f94,
        wireframe: false,
        transparent: true,
        opacity: 1,
        side: THREE.DoubleSide //两面可见
      }); //材质对象Material

      var matArrayPlane = new THREE.MeshLambertMaterial({
        color: 0x95220e,
        wireframe: false,
        transparent: true,
        opacity: 0.95,
        side: THREE.DoubleSide //两面可见
      }); //材质对象Material



      var wall = this.returnWallObject(400, 30, 1, 0, matArrayB, 0, 15, 150, "墙面");
      var door_cube1 = this.returnWallObject(20, 28, 1, 0, matArrayB, 0, 10, 150, "前门1");
      var objects_cube = [];
      objects_cube.push(door_cube1);
      this.createResultBsp(wall, objects_cube);
      this.createDoor_left(10, 24, 1, 1, 45, 12, 150, "左门1");
      this.createDoor_right(10, 24, 1, 1, -45, 12, 150, "右门1");



      this.createWall(1,30,150,matArrayB,0,15,-75)

      //中间的墙
      var wall5 = this.returnWallObject(400, 30, 1, 0, matArrayB, 0, 15, 0, "墙面");
      var door_cube2 = this.returnWallObject(20, 28, 1, 0, matArrayB, -50, 10, 0, "前门2");
      var door_cube3 = this.returnWallObject(20, 28, 1, 0, matArrayB, 50, 10, 0, "前门3");
      var objects_cube1 = [];
      objects_cube1.push(door_cube2);
      objects_cube1.push(door_cube3);
      this.createResultBsp(wall5, objects_cube1);
      this.createDoor_left(10, 24, 1, 1, -5, 12, 0, "左门1");
      this.createDoor_right(10, 24, 1, 1, -95, 12, 0, "右门1");
      this.createDoor_left(10, 24, 1, 1, 95, 12, 0, "左门1");
      this.createDoor_right(10, 24, 1, 1, 5, 12, 0, "右门1");

      //最后面的墙
      var wall4 = this.returnWallObject(400, 30, 1, 0, matArrayB, 0, 15, -150, "墙面");
      var door_cube4 = this.returnWallObject(20, 20, 1, 0, matArrayB, -100, 16, -150, "前门2");
      var door_cube5 = this.returnWallObject(20, 20, 1, 0, matArrayB, 100, 16, -150, "前门3");
      var objects_cube3 = [];
      objects_cube3.push(door_cube4);
      objects_cube3.push(door_cube5);
      this.createResultBsp(wall4, objects_cube3);
      this.createWindow(20, 20, 1, 1, -100, 16, -150, "窗户1");
      this.createWindow(20, 20, 1, 1, 100, 16, -150, "窗户2");



      var gltfLoader = new GLTFLoader()
      gltfLoader.load( //文件加载 文件地址 我用的本地文件模拟的 vue 必须把文件放入public目录
        // 否则文件会被编译
        // 我是放入了public目录的model文件 说以地址为${process.env.BASE_URL}model
        `/static/3d/bottom/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.5,0.5,0.5);//网格模型缩放
          gltf.scene.position.set(190, 0, 10) //定位
          gltf.scene.rotation.y = -Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          // 模型是否否需要阴影
          // gltf.scene.traverse(obj => {
          //   obj.castShadow = true
          //   obj.receiveShadow = true
          // })
          this.scene.add(gltf.scene)
          // //遍历当前模型的动画
          // gltf.animations.forEach(i => {
          //   this.state.push(i.name)
          //   this[i.name] = this.animationMixer.clipAction(i)
          // })
          // // 执行第一个动画
          // this[gltf.animations[0].name].play()
        }
      )



      gltfLoader.load(
        `/static/3d/hand_truck_cart/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.1,0.1,0.1);//网格模型缩放
          gltf.scene.position.set(190, 7, 20) //定位
          gltf.scene.rotation.y = -Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )


      gltfLoader.load(
        `/static/3d/truck/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.2,0.2,0.2);//网格模型缩放
          gltf.scene.position.set(140, 0, 163) //定位
          gltf.scene.rotation.y = Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )



      gltfLoader.load(
        `/static/3d/hand_truck_cart/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.1,0.1,0.1);//网格模型缩放
          gltf.scene.position.set(190, 7, 20) //定位
          gltf.scene.rotation.y = -Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )

      gltfLoader.load(
        `/static/3d/forklift_truck/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.08,0.08,0.08);//网格模型缩放
          gltf.scene.position.set(50, 12, 50) //定位
          gltf.scene.rotation.y = -Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )

      gltfLoader.load(
        `/static/3d/forklift_truck/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.08,0.08,0.08);//网格模型缩放
          gltf.scene.position.set(120, 12, 80) //定位
          gltf.scene.rotation.y = Math.PI //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )


      gltfLoader.load(
        `/static/3d/box/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.08,0.08,0.08);//网格模型缩放
          gltf.scene.position.set(120, 10.5, 98) //定位
          gltf.scene.rotation.y = Math.PI/2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )


      gltfLoader.load(
        `/static/3d/male/scene.gltf`,
        gltf => {
          // gltf.scene.name = newName
          gltf.scene.scale.set(0.2,0.2,0.2);//网格模型缩放
          gltf.scene.position.set(130, 2, 94) //定位
          gltf.scene.rotation.y = Math.PI * 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )



      this.createGrass(426, 1, 10, 0, 12.5, 0, 179.5, '草地1')
      this.createRoad(401, 1, 25, 0, 0.5, 0, 162, '路1')
      this.createRoad2(25, 1, 350, 0, 213, 0, -0.5, '路2')



      //货架制作
      this.createWall(0.5,40,0.5,matArrayGood,-177,20,40)//柱子
      this.createWall(0.5,40,0.5,matArrayGood,-190,20,40)
      this.createWall(0.5,40,0.5,matArrayGood,-177,20,130)
      this.createWall(0.5,40,0.5,matArrayGood,-190,20,130)

      this.createWall(15,0.5,92,matArrayPlane,-183.5,30,85)//平面
      this.createWall(15,0.5,92,matArrayPlane,-183.5,15,85)//平面


      this.createWall(0.5,40,0.5,matArrayGood,-147,20,40)//柱子
      this.createWall(0.5,40,0.5,matArrayGood,-160,20,40)
      this.createWall(0.5,40,0.5,matArrayGood,-147,20,130)
      this.createWall(0.5,40,0.5,matArrayGood,-160,20,130)

      this.createWall(15,0.5,92,matArrayPlane,-153.5,30,85)//平面
      this.createWall(15,0.5,92,matArrayPlane,-153.5,15,85)//平面


      this.createWall(0.5,40,0.5,matArrayGood,-117,20,40)//柱子
      this.createWall(0.5,40,0.5,matArrayGood,-130,20,40)
      this.createWall(0.5,40,0.5,matArrayGood,-117,20,130)
      this.createWall(0.5,40,0.5,matArrayGood,-130,20,130)

      this.createWall(15,0.5,92,matArrayPlane,-123.5,30,85)//平面
      this.createWall(15,0.5,92,matArrayPlane,-123.5,15,85)//平面

      this.createWall(0.5,40,0.5,matArrayGood,-87,20,40)//柱子
      this.createWall(0.5,40,0.5,matArrayGood,-100,20,40)
      this.createWall(0.5,40,0.5,matArrayGood,-87,20,130)
      this.createWall(0.5,40,0.5,matArrayGood,-100,20,130)

      this.createWall(15,0.5,92,matArrayPlane,-93.5,30,85)//平面
      this.createWall(15,0.5,92,matArrayPlane,-93.5,15,85)//平面







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
      this.camera.position.set(300, 100, 160); //设置相机位置
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
        // this.scene.rotateY(0.01);//每次绕y轴旋转0.01弧度
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
