<template>
  <div id="demoS" @click="demo17">

  </div>
</template>

<script>
  // import * as THREE from 'three';
  var three = require('three')
  import "three/examples/js/controls/OrbitControls"

  export default {
    name: 'Demo2',
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
      let module = document.getElementById('demoS')
      module.appendChild(this.renderer.domElement);
      // document.body.appendChild(this.renderer.domElement); //body元素中插入canvas对象
      //执行渲染操作   指定场景、相机作为参数
      //renderer.render(scene, camera);

      // 球体网格模型
      var geometry2 = new THREE.SphereGeometry(60, 40, 40);
      var material2 = new THREE.MeshLambertMaterial({
        color: 0xF4606C,//材质颜色，比如蓝色0x0000ff
        opacity:0.7,//透明度设置，0表示完全透明，1表示完全不透明
        transparent:true//是否开启透明，默认false
      });
      var mesh2 = new THREE.Mesh(geometry2, material2); //网格模型对象Mesh
      mesh2.translateY(120); //球体网格模型沿Y轴正方向平移120
      this.scene.add(mesh2);

// 圆柱网格模型
      var geometry3 = new THREE.CylinderGeometry(50, 50, 100, 25);
      // var material3 = new THREE.MeshLambertMaterial({//漫反射填充模型
      //   color: 0xffff00,
      //   wireframe: false, //将几何图形渲染为线框。 默认值为false
      //   shininess:12
      // });
      var sphereMaterial=new THREE.MeshPhongMaterial({//镜面反射填充模型
        color:0xffff00,
        specular:0x4488ee,//表示球体网格模型的高光颜色
        shininess:12//光照强度的系数
      });//材质对象
      var mesh3 = new THREE.Mesh(geometry3, sphereMaterial); //网格模型对象Mesh
// mesh3.translateX(120); //球体网格模型沿Y轴正方向平移120
      mesh3.position.set(120,0,0);//设置mesh3模型对象的xyz坐标为120,0,0
      this.scene.add(mesh3); //

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
        //this.mesh.rotateY(0.01);//每次绕y轴旋转0.01弧度
        //requestAnimationFrame(this.renderA);
      },
      demo17(event){
        event.preventDefault();
        // console.log(event.clientX,event.clientY)


        // 声明 raycaster 和 mouse 变量
        var raycaster = new THREE.Raycaster();
        var mouse = new THREE.Vector2();

        // 通过鼠标点击位置,计算出 raycaster 所需点的位置,以屏幕为中心点,范围 -1 到 1
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;



        //通过鼠标点击的位置(二维坐标)和当前相机的矩阵计算出射线位置
        let camera = this.camera
        raycaster.setFromCamera(mouse, camera);

        // 获取与射线相交的对象数组，其中的元素按照距离排序，越近的越靠前
        var intersects = raycaster.intersectObjects(this.scene.children);

        //返回选中的对象
        //var intersects = getIntersects(event);

        // 获取选中最近的 Mesh 对象
        if (intersects.length != 0 && intersects[0].object instanceof THREE.Mesh) {
          console.log(intersects)
          let selectObject = intersects[0].object;
          this.changeMaterial(selectObject);
          //this.camera.position.set(intersects[0].point.x + 100, intersects[0].point.y + 100, intersects[0].point.z + 100); //设置相机位置
        } else {
          // console.log("未选中 Mesh!");
        }
      },
      changeMaterial(object) {

        var material = new THREE.MeshLambertMaterial({
          color: 0xffffff * Math.random(),
          transparent: object.material.transparent ? false : true,
          opacity: 0.8
        });
        object.material = material;
        this.renderA()
      }
    }
  }
</script>

<style scoped>

</style>
