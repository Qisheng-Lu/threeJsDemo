<template>
  <div>

  </div>
</template>

<script>
  // import * as THREE from 'three';
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"

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
      //var geometry = new THREE.SphereGeometry(60, 40, 40); //创建一个球体几何对象
      this.geometry = new THREE.BoxGeometry(100, 100, 100); //创建一个立方体几何对象Geometry
      this.material = new THREE.MeshLambertMaterial({
        color: 0x5bd491
      }); //材质对象Material
      this.mesh = new THREE.Mesh(this.geometry, this.material); //网格模型对象Mesh
      this.scene.add(this.mesh); //网格模型添加到场景中
      /**
       * 光源设置
       */
      //点光源
      this.point = new THREE.PointLight(0x444444);
      this.point.position.set(400, 200, 300); //点光源位置
      this.scene.add(this.point); //点光源添加到场景中

      // 点光源2  位置和point关于原点对称
      // var point2 = new THREE.PointLight(0x444444);
      // point2.position.set(-400, -200, -300); //点光源位置
      // this.scene.add(point2); //点光源添加到场景中
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





      // var box=new THREE.SphereGeometry(60,40,40);//创建一个球体几何对象

      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);


//间隔20ms周期性调用函数fun,20ms也就是刷新频率是50FPS(1s/20ms)，每秒渲染50次
      //setInterval(this.renderA,100);
      //this.renderA(renderer,mesh,scene,camera)
      this.renderA()
      var controls = new THREE.OrbitControls(this.camera,this.renderer.domElement);//创建控件对象
      controls.addEventListener('change', this.renderA);//监听鼠标、键盘事件
    },
    methods: {
      renderA() {
        this.renderer.render(this.scene,this.camera);//执行渲染操作
        this.mesh.rotateY(0.01);//每次绕y轴旋转0.01弧度
        requestAnimationFrame(this.renderA);
      }
    }
  }
</script>

<style scoped>

</style>
