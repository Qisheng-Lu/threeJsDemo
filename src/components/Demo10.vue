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
        ground: null,
      }
    },
    created() {
      let urlTemp = "assets/wall.jpg";
      this.imgUrl = require("@/" + urlTemp)
      this.scene = new THREE.Scene();

      this.ground = new THREE.BufferGeometry(); //创建一个Buffer类型几何体对象
//类型数组创建顶点数据
      var vertices = new Float32Array([
        0, 0, 0,
        150, 0, 0,
        0, 0, 150,
        150, 0, 150,
        0,0,-150,
        150,0,-150,
      ]);
// 创建属性缓冲区对象
      var attribue = new THREE.BufferAttribute(vertices, 3); //3个为一组，表示一个顶点的xyz坐标
// 设置几何体attributes属性的位置属性
      this.ground.attributes.position = attribue;
      var indexes = new Uint16Array([
        0,1,2,
        1,2,3,
        0,1,4,
        1,4,5,
      ])
// 索引数据赋值给几何体的index属性
      this.ground.index = new THREE.BufferAttribute(indexes, 1); //1个为一组

      // for (var i=0;i<20;i++){
      //   var line=new THREE.Line(this.geometry,new THREE.LineBasicMaterial({color:0x000000,opacity:0.2}));
      //   line.position.z=(i*50)-500;
      //   this.scene.add(line);
      //
      //   var line=new THREE.Line(this.geometry,new THREE.LineBasicMaterial({color:0x000000,opacity:0.2}));
      //   line.position.x=(i*50)-500;
      //   line.rotation.y=90*Math.PI/180;
      //   this.scene.add(line);
      // }

      var material = new THREE.MeshBasicMaterial({//面渲染模型,颜色过渡
        color: 0xcccccc, //以顶点颜色为准
        transparent: true,
        opacity: 0.8,
        side: THREE.DoubleSide //两面可见
      }); //材质对象

      var mesh = new THREE.Mesh(this.ground, material); //网格模型对象Mesh
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
      this.renderer = new THREE.WebGLRenderer({
        //增加以下两个属性来抗锯齿
        antialias:true,
        alpha:true
      });
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
