<template>
  <div>

  </div>
</template>

<script>
  // import * as THREE from 'three';
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"

  export default {
    name: 'Demo999',
    data() {
      return {
        imgUrl: null,
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
      }
    },
    created() {
      let urlTemp = "assets/wall.jpg";
      this.imgUrl = require("@/" + urlTemp)
      this.scene = new THREE.Scene();




      //this.geometry = new THREE.BoxGeometry(500, 500, 10); //立方体


//       var textureLoader = new THREE.TextureLoader(); // 纹理加载器
      //var texture = textureLoader.load(this.imgUrl); // 加载图片，返回Texture对象
      //var textureBump = textureLoader.load(this.imgUrl);
// 材质对象2
      //var material_2 = new THREE.MeshLambertMaterial({
        //map: texture, // 设置纹理贴图
        //bumpMap:textureBump,//凹凸贴图
        //bumpScale:3,//设置凹凸高度，默认值1。
        // wireframe:true,
      //});
// 设置材质数组
      //var materialArr = [material_2,material_2,material_2,material_2,material_2,material_2,material_2];

// 设置数组材质对象作为网格模型材质参数


      this.geometry=new THREE.Geometry();
      //几何体中加入两个顶点
      this.geometry.vertices.push(new THREE.Vector3(-500,0,0));
      this.geometry.vertices.push(new THREE.Vector3(500,0,0));

      for (var i=0;i<20;i++){
        var line=new THREE.Line(this.geometry,new THREE.LineBasicMaterial({color:0x000000,opacity:0.2}));
        line.position.z=(i*50)-500;
        this.scene.add(line);

        var line=new THREE.Line(this.geometry,new THREE.LineBasicMaterial({color:0x000000,opacity:0.2}));
        line.position.x=(i*50)-500;
        line.rotation.y=90*Math.PI/180;
        this.scene.add(line);
      }

      var material = new THREE.MeshBasicMaterial({//面渲染模型,颜色过渡
        color: 0x0000ff, //以顶点颜色为准
        side: THREE.DoubleSide //两面可见
      }); //材质对象

      var mesh = new THREE.Mesh(this.geometry, material); //网格模型对象Mesh
      this.scene.add(mesh); //网格模型添加到场景中

      /**
       * 光源设置
       */
      //点光源
      this.point = new THREE.PointLight(0x444444);
      this.point.position.set(400, 200, 300); //点光源位置
      this.scene.add(this.point); //点光源添加到场景中

      //环境光
      this.ambient = new THREE.AmbientLight(0x444444);
      this.scene.add(this.ambient);

      /**
       * 相机设置
       */
      this.width = window.innerWidth; //窗口宽度
      this.height = window.innerHeight; //窗口高度
      var k = this.width / this.height; //窗口宽高比
      var s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
      //创建相机对象
      this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
      this.camera.position.set(200, 200, 200); //设置相机位置
      this.camera.lookAt(this.scene.position); //设置相机方向(指向的场景对象)
      /**
       * 创建渲染器对象
       */
      this.renderer = new THREE.WebGLRenderer();
      this.renderer.setSize(this.width, this.height);//设置渲染区域尺寸
      this.renderer.setClearColor(0x0c5460, 1); //设置背景颜色
      document.body.appendChild(this.renderer.domElement); //body元素中插入canvas对象
      //执行渲染操作   指定场景、相机作为参数
      //renderer.render(scene, camera);

      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);

//间隔20ms周期性调用函数fun,20ms也就是刷新频率是50FPS(1s/20ms)，每秒渲染50次
      //setInterval(this.renderA,100);
      //this.renderA(renderer,mesh,scene,camera)
      this.renderA()
      var controls = new THREE.OrbitControls(this.camera, this.renderer.domElement);//创建控件对象
      controls.addEventListener('change', this.renderA);//监听鼠标、键盘事件
    },
    methods: {
      renderA() {
        this.renderer.render(this.scene, this.camera);//执行渲染操作
      }
    }
  }
</script>

<style scoped>

</style>
