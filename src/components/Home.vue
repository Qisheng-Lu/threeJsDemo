<template>
  <div>
    <div class="container">
      <router-link tag="a" target="_blank" to="/Demo1">一个匀速旋转的正方体</router-link>
      <router-link tag="a" target="_blank" to="/Demo2">三个物体</router-link>
      <router-link tag="a" target="_blank" to="/Demo3">点、线、面渲染模型（大的几何体由无数三角形组成）</router-link>
      <router-link tag="a" target="_blank" to="/Demo4">颜色过渡</router-link>
      <router-link tag="a" target="_blank" to="/Demo5">点模型</router-link>
      <router-link tag="a" target="_blank" to="/Demo6">一个盒子（纹理贴图）</router-link>
      <router-link tag="a" target="_blank" to="/Demo7">纹理贴图-分面</router-link>
      <router-link tag="a" target="_blank" to="/Demo8">纹理贴图-凹凸-砖面</router-link>
      <router-link tag="a" target="_blank" to="/Demo9">平面网格</router-link>
      <router-link tag="a" target="_blank" to="/Demo10">三角形组成平面</router-link>
      <router-link tag="a" target="_blank" to="/Demo11">一个简易的仓库</router-link>
      <router-link tag="a" target="_blank" to="/Demo12">一个简易的仓库-里面有卡车，手推车</router-link>
      <router-link tag="a" target="_blank" to="/Demo13">一个简易的仓库-里面有卡车，手推车，叉车，公路，草地，人，盒子等</router-link>
      <router-link tag="a" target="_blank" to="/Demo14">导入.gltf文件仓库</router-link>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Home',
    data() {
      return {

      }
    },
    created() {

    },
    methods: {

    }
  }
</script>

<style scoped>
  .container{

  }
  .container a{
    display: block;
    text-align: left;
    margin: 20px 0 0 20px;
  }
</style>
