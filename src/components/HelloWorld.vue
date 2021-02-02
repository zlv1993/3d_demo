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

     //生化罐
      this.createModel('/model/biochemicalTank.obj','/model/biochemicalTank.mtl',{x:0,y:76,z:-100,scale:0.05},'biochemicalTank')

      // 鼓风机
      this.createModel('/model/gufengji.obj','/model/gufengji.mtl',{x:140,y:30,z:0,scale:0.1},'gufengji')

      //建筑
       this.createModel('/model/jianzhu.obj','/model/jianzhu.mtl',{x:400,y:30,z:-600,scale:0.1,rotateY:Math.PI},'jianzu')
       
       setTimeout(()=>{  
       let jianzu =this.scene.getObjectByName("jianzu").clone()
       jianzu.position.set(400,30,-250)
       jianzu.name='建筑2'
       this.scene.add(jianzu)
       },3000)
       //进水泵
       this.createModel('/model/jinshuiben.obj','/model/jinshuiben.mtl',{x:-400,y:30,z:-400,scale:0.1,rotateY:Math.PI},'jinshuiben')
             //冷却塔
      this.createModel('/model/lengqueta.obj','/model/lengqueta.mtl',{x:0,y:30,z:0,scale:0.1},'lengqueta')

         //MBR
       this.createModel('/model/MBR.obj','/model/MBR.mtl',{x:-400,y:30,z:-200,scale:0.1,rotateY:Math.PI},'MBR') 
       //纳滤新
       this.createModel('/model/nalvnew.obj','/model/nalvnew.mtl',{x:-400,y:30,z:0,scale:0.1,rotateY:Math.PI},'nalvnew')

        //轻量化超滤
       this.createModel('/model/qinglianghuachaolv.obj','/model/qinglianghuachaolv.mtl',{x:-400,y:30,z:200,scale:0.1,rotateY:Math.PI},'qinglianghuachaolv')


       //轻量化超滤
       this.createModel('/model/qinglianghuachaolv.obj','/model/qinglianghuachaolv.mtl',{x:-400,y:30,z:200,scale:0.1,rotateY:Math.PI},'qinglianghuachaolv')


      // 清液池子
      this.createModel('/model/qingyechi.obj','/model/qingyechi.mtl',{x:430,y:30,z:400,scale:0.1},'qingyechi')


      
      // 清液罐
      this.createModel('/model/qingyeguan.obj','/model/qingyeguan.mtl',{x:230,y:30,z:600,scale:0.1},'qingyeguan')


      // 射流泵
      this.createModel('/model/sheliubeng.obj','/model/sheliubeng.mtl',{x:230,y:30,z:400,scale:0.1},'sheliubeng')

        // 超滤
      this.createModel('/model/ultrafiltration.obj','/model/ultrafiltration.mtl',{x:230,y:30,z:100,scale:0.1},'ultrafiltration')






      this.render()
      this.initControl()
    },
    createFloor(){//创建地板
      let floor=new THREE.PlaneGeometry(3500,2000,30)
      let textLoader=new THREE.TextureLoader()
      textLoader.load('/img/back.jpg',(texture)=>{
        texture.wrapS=texture.wrapT=THREE.RepeatWrapping
        texture.repeat.set(5,5)
        let floorMaterial=new THREE.MeshLambertMaterial({
              map:texture,
              side:THREE.DoubleSide,
              transparent:true,
              opacity:0.7,
              emissive: '#081838',
              emissiveIntensity: 0.15,

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
      this.camera = new THREE.PerspectiveCamera(45, innerHeigt / innerWidth, 1, 10000 );
      this.camera.position.set(0, 600, 750);
      
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
     // requestAnimationFrame(this.render)
    },
    initLight(){
       let directionalLight = new THREE.DirectionalLight(0xFFFFFF, 0.15);//模拟远处类似太阳的光源
            // 设置阴影
            directionalLight.color.setHSL(0.1, 1, 0.95);
            directionalLight.position.set(0, 1000, -1000).normalize();
            directionalLight.castShadow = true
             this.scene.add(directionalLight);

            let directionalLight1 = new THREE.DirectionalLight(0xFFFFFF, 0.15);//模拟远处类似太阳的光源
            // 设置阴影
            directionalLight1.color.setHSL(0.1, 1, 0.95);
            directionalLight1.position.set(0, 1000, 1000).normalize();
            directionalLight1.castShadow = true
             this.scene.add(directionalLight1);

            let directionalLight2 = new THREE.DirectionalLight(0xFFFFFF, 0.15);//模拟远处类似太阳的光源
            // 设置阴影
            directionalLight2.color.setHSL(0.1, 1, 0.95);
            directionalLight2.position.set(0, 100, -1000).normalize();
            directionalLight2.castShadow = true
             this.scene.add(directionalLight2);

            let directionalLight3 = new THREE.DirectionalLight(0xFFFFFF, 0.15);//模拟远处类似太阳的光源
            // 设置阴影
            directionalLight3.color.setHSL(0.1, 1, 0.95);
            directionalLight3.position.set(0, 100, 1000).normalize();
            directionalLight3.castShadow = true
             this.scene.add(directionalLight3);

            // var directionalLight4 = new THREE.DirectionalLight(0xFFFFFF, 0.10);//模拟远处类似太阳的光源
            // // 设置阴影
            // directionalLight4.color.setHSL(0.1, 1, 0.95);
            // directionalLight4.position.set(0, 1000, 0).normalize();
            // directionalLight4.castShadow = true
            // scene.add(directionalLight4);


            // var helper = new THREE.CameraHelper( directionalLight.shadow.camera );
            // scene.add( helper );    

            let ambient = new THREE.AmbientLight(0xffffff, 0.9); //AmbientLight,影响整个场景的光源
            ambient.position.set(0, 0, 0);
            this.scene.add(ambient);
    },
     createModel(objUrl,mtlUrl,params,name){
       let objLoader=  new OBJLoader();
       let mtlLoader=  new MTLLoader()
        mtlLoader.load(mtlUrl,(material)=>{
          objLoader.setMaterials(material)
          objLoader.load(objUrl,(mesh)=>{
               mesh.name=name
               let scale=params.scale
               mesh.scale.set(scale,scale,scale)
               if(params.rotateY){
                   mesh.rotateY(params.rotateY)
               }
               mesh.position.x=params.x
               mesh.position.y=params.y
              mesh.position.z=params.z
              this.scene.add(mesh)
          })

       })


    },
  
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
