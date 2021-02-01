<template>
    <div id ="three_content"></div>
</template>

<script>
import * as THREE from 'three';
 import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls'
 import { OBJLoader, MTLLoader } from 'three-obj-mtl-loader'
import func from '../../vue-temp/vue-editor-bridge';
export default {

  data(){
    return {
      camera:'',
      scene:'',
      renderer:'',
      contorls:null
    }
  },
  mounted(){
    this.init()
  },
  methods:{
    init() {

      let container = document.getElementById('three_content');
            
      let innerWidth=container.clientWidth
      let innerHeigt=container.clientHeight 
      this.camera = new THREE.PerspectiveCamera( 70, innerHeigt / innerWidth, 0.01, 10000 );
      this.camera.position.set(300, 300, 300);
      
      this.scene = new THREE.Scene();
      this.light()

      let geometry = new THREE.BoxGeometry( 100, 100, 100 );
      let material = new THREE.MeshNormalMaterial({
        color: 0x0000ff
      });

      let  mesh = new THREE.Mesh( geometry, material );
      this.scene.add( mesh );
      this.renderer = new THREE.WebGLRenderer( );
      this.renderer.setSize( innerWidth, innerHeigt );
      this.renderer.setClearColor(0xb9d3ff, 1)
      this.camera.lookAt(this.scene.position)
      console.log(this.scene)
      container.appendChild(this.renderer.domElement);
      this.createModel('/src/static/jianzu/jianzhu.obj','/src/static/jianzu/jianzhu.mtl','build')
      this.render()
      this.controls = new OrbitControls(this.camera, this.renderer.domElement)
      this.controls.addEventListener('change', this.render)
    },
    initScene(){

    },
    render(){
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.render)
    },
    light(){
       let  ambient = new THREE.AmbientLight(0xffffff);
       this.scene.add(ambient);//环境光对象添加到scene场景中

       let point = new THREE.PointLight(0xffffff);
        point.position.set(400, 200, 300); //点光源位置
       this.scene.add(point); //点光源添加到场景中
    },
    async createModel(objUrl,mtlUrl,name){
       let objLoader=  new OBJLoader();
       let mtlLoader=  new MTLLoader()
       await mtlLoader.load(objUrl,(material)=>{
          objLoader.setMaterials(material)
       })
       await objLoader.load(mtlUrl,(mesh)=>{
         mesh.name=name
        this.scene.add(mesh)
       })


    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
#three_content{
height: 100%;
width: 100%;
}
</style>
