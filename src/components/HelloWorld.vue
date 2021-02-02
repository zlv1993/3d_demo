<template>
    <div id ="three_content"></div>
</template>

<script>
import * as THREE from 'three';
 import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls'
import {OBJLoader} from 'three/examples/jsm/loaders/OBJLoader'
import {MTLLoader} from 'three/examples/jsm/loaders/MTLLoader'
import * as dat from 'dat.gui';
import Stats from 'stats.js'


export default {
  data(){
    return {
      camera:'',
      scene:'',
      renderer:'',
      stats :'',
      contorls:null
    }
  },
  mounted(){
    this.init()
  },
  methods:{
    init() {
      this.initScene()
      this.initLight()
      this.initAxis()
      this.initRender()
      this.createFloor()
      //冷却塔
      this.createModel('/model/lengqueta.obj','/model/lengqueta.mtl',{x:0,y:30,z:0,scale:0.1},'lengqueta')
      //生化罐
      this.createModel('/model/biochemicalTank.obj','/model/biochemicalTank.mtl',{x:0,y:76,z:-100,scale:0.05},'biochemicalTank')
      this.render()
      this.initControl()
    },
    createFloor(){//创建地板
      let floor=new THREE.PlaneGeometry(1000,1000,10,10)
      let textLoader=new THREE.TextureLoader()
      textLoader.load('/img/back.jpg',(texture)=>{
        texture.wrapS=texture.wrapT=THREE.RepeatWrapping
        texture.repeat.set(5,5)
        let floorMaterial=new THREE.MeshLambertMaterial({
              map:texture,
              side:THREE.DoubleSide,
              transparent:true,
              opacity:0.7
         })
         let mesh=new THREE.Mesh(floor,floorMaterial)
         mesh.receiveShadow=true
         mesh.rotation.x=Math.PI/2
         this.scene.add(mesh)
      })
      

    },

    initAxis(){
      ///添加辅助
     let axis=new THREE.AxisHelper(500)
     this.scene.add(axis)

     //状态值
     let stats=new Stats();
     stats.domElement.style.position = 'absolute'; //绝对坐标  
     stats.domElement.style.left = '0px';// (0,0)px,左上角  
     stats.domElement.style.top = '0px';  
     document.getElementById('three_content').appendChild(stats.domElement);  
     this.stats=stats
     const params={
       x:0,
       y:50,
       z:0
     }
     const gui = new dat.GUI();
       gui.add(params, 'y', 50, 100)
        .onChange( (val)=> {
          let mesh=this.scene.getObjectByName ( "biochemicalTank" );
          mesh.position.y=val
       });

    },
    initScene(){//渲染场景
      let container = document.getElementById('three_content');
      let innerWidth=container.clientWidth
      let innerHeigt=container.clientHeight 
      this.camera = new THREE.PerspectiveCamera(45, innerHeigt / innerWidth, 1, 5000 );
      this.camera.position.set(300, 400, 500);
      
      this.scene = new THREE.Scene();
    },
    initControl(){//渲染鼠标事件
      this.controls = new OrbitControls(this.camera, this.renderer.domElement)
      this.controls.addEventListener('change', this.render)
    },
    initRender(){///初始化渲染器
      let container = document.getElementById('three_content');
      let innerWidth=container.clientWidth
      let innerHeigt=container.clientHeight 
       this.renderer = new THREE.WebGLRenderer( );
      this.renderer.setSize( innerWidth, innerHeigt );
      this.renderer.setClearColor(0xb9d3ff, 1)
      this.camera.lookAt(this.scene.position)
      container.appendChild(this.renderer.domElement);
    },
    render(){///重新渲染
      this.renderer.render(this.scene, this.camera);
      this.stats.update()
      requestAnimationFrame(this.render)
    },
    initLight(){
       let  ambient = new THREE.AmbientLight(0xffffff);
       this.scene.add(ambient);//环境光对象添加到scene场景中

       let point = new THREE.PointLight(0xffffff);
        point.position.set(400, 200, 300); //点光源位置
       this.scene.add(point); //点光源添加到场景中
    },
    async createModel(objUrl,mtlUrl,params,name){
       let objLoader=  new OBJLoader();
       let mtlLoader=  new MTLLoader()
       await mtlLoader.load(mtlUrl,(material)=>{
          objLoader.setMaterials(material)

       })
       await   objLoader.load(objUrl,(mesh)=>{
               mesh.name=name
               let scale=params.scale
               mesh.scale.set(scale,scale,scale)
               mesh.position.x=params.x
               mesh.position.y=params.y
              mesh.position.z=params.z
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
