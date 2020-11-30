<template>
  <div>
    <div id="demo" @click="test"></div>
  </div>
</template>

<script>
  // import * as THREE from 'three';
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"
  import {GLTFLoader} from 'three/examples/jsm/loaders/GLTFLoader'


  export default {
    name: 'Demo17',
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
    mounted() {
      /**
       * 创建场景对象Scene
       */
      this.scene = new THREE.Scene();
      /**
       * 创建网格模型
       */
      var gltfLoader = new GLTFLoader()
      gltfLoader.load(
        `/static/3d/bottom/scene.gltf`,
        gltf => {
          gltf.scene.name = '一个破损的罐子'
          gltf.scene.scale.set(10, 10, 10);//网格模型缩放
          gltf.scene.position.set(0, 0, 0) //定位
          gltf.scene.rotation.y = -Math.PI / 2 //转动 这些有点类似canvas 或者 c3的动画
          this.scene.add(gltf.scene)
        }
      )

      this.geometry = new THREE.BoxGeometry(50, 50, 50); //创建一个立方体几何对象Geometry
      this.material = new THREE.MeshLambertMaterial({
        color: 0x5bd491
      }); //材质对象Material
      this.mesh = new THREE.Mesh(this.geometry, this.material); //网格模型对象Mesh
      this.mesh.position.set(100,0,0)
      this.scene.add(this.mesh); //网格模型添加到场景中
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
      let module = document.getElementById('demo')
      module.appendChild(this.renderer.domElement);

      var axisHelper = new THREE.AxisHelper(250);
      this.scene.add(axisHelper);


      var controls = new THREE.OrbitControls(this.camera, this.renderer.domElement);//创建控件对象
      controls.addEventListener('change', this.renderA);//监听鼠标、键盘事件
      this.renderA()
      this.renderA()
    },
    methods: {
      renderA() {
        this.renderer.render(this.scene, this.camera);//执行渲染操作
      },
      test(event) {
        event.preventDefault();
        var raycaster = new THREE.Raycaster();
        var mouse = new THREE.Vector2();

        // 通过鼠标点击位置,计算出 raycaster 所需点的位置,以屏幕为中心点,范围 -1 到 1
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

        //通过鼠标点击的位置(二维坐标)和当前相机的矩阵计算出射线位置
        let camera = this.camera
        raycaster.setFromCamera(mouse, camera);

        // 获取与射线相交的对象数组，其中的元素按照距离排序，越近的越靠前
        console.log(this.scene)
        var intersects = raycaster.intersectObjects(this.scene.children);

        console.log(intersects, 'intersects')
        // 获取选中最近的 Mesh 对象
        if (intersects.length != 0 && intersects[0].object instanceof THREE.Mesh) {
          let selectObject = intersects[0].object;

        } else {

        }
      },
    }
  }
</script>

<style scoped>
  #demo {
    width: 100%;
    height: 100%;
    background-color: #fff;
  }
</style>
