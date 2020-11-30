<template>
  <div>

  </div>
</template>

<script>
  // import * as THREE from 'three';
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"
  import "@/assets/js/StlLoader.js"
  //import { STLLoader } from 'three'

  export default {
    name: 'HelloWorld',
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
      }
    },
    created() {
      /**
       * 创建场景对象Scene
       */
      this.scene = new THREE.Scene();
      /**
       * 创建网格模型
       */

      var loader1 = new THREE.STLLoader();
      var _this = this
      loader1.load('/static/3d/aaa.STL', function (geometry) {
        //console.log(geometry)
        var mat = new THREE.MeshLambertMaterial({ color: '#ccc' });//{ color: 0x00ffff }
        var mesh = new THREE.Mesh(geometry, mat);
        mesh.rotation.x = Math.PI; //将模型摆正
        //mesh.rotation.y = Math.PI/4; //将模型摆正
        mesh.rotation.z = Math.PI/2; //将模型摆正
        mesh.scale.set(0.003, 0.003, 0.003); //缩放
        geometry.center(); //居中显示
        _this.scene.add(mesh);
      });

      //var geometry = new THREE.SphereGeometry(60, 40, 40); //创建一个球体几何对象
      // this.geometry = new THREE.BoxGeometry(100, 100, 100); //创建一个立方体几何对象Geometry
      // this.material = new THREE.MeshLambertMaterial({
      //   color: 0x5bd491
      // }); //材质对象Material
      // this.mesh = new THREE.Mesh(this.geometry, this.material); //网格模型对象Mesh
      // this.scene.add(this.mesh); //网格模型添加到场景中
      /**
       * 光源设置
       */
      //点光源
      this.point = new THREE.PointLight(0x444444);
      this.point.position.set(400, 200, 300); //点光源位置
      this.scene.add(this.point); //点光源添加到场景中
     // var point1 = new THREE.PointLight(0x444444);
     //  point1.position.set(0, 500, 0); //点光源位置
     //  this.scene.add(point1); //点光源添加到场景中

      //环境光
      this.ambient = new THREE.AmbientLight(0x444444);
      this.scene.add(this.ambient);
      // console.log(scene)
      // console.log(scene.children)



      /**
       * 相机设置
       */
      this.width = window.innerWidth; //窗口宽度
      this.height = window.innerHeight; //窗口高度
      // var k = this.width / this.height; //窗口宽高比
      // var s = 150; //三维场景显示范围控制系数，系数越大，显示的范围越大
      //创建相机对象
      //this.camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);//正投影相机
      this.camera = new THREE.PerspectiveCamera(60, this.width / this.height, 1, 1000);//透视投影相机
      this.camera.position.set(200, 300, 200); //设置相机位置
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





      // var box=new THREE.SphereGeometry(60,40,40);//创建一个球体几何对象

      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);


      this.renderA()
      setTimeout(this.renderA,100)
      var controls = new THREE.OrbitControls(this.camera,this.renderer.domElement);//创建控件对象
      controls.addEventListener('change', this.renderA);//监听鼠标、键盘事件
    },
    methods: {
      renderA() {
        this.renderer.render(this.scene,this.camera);//执行渲染操作
        // this.mesh.rotateY(0.01);//每次绕y轴旋转0.01弧度
        // requestAnimationFrame(this.renderA);
      }
    }
  }
</script>

<style scoped>

</style>
