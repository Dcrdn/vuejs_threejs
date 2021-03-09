<template>
  <div id="container"></div>
</template>
<script type ='module'>
// import * as THREE from 'https://unpkg.com/three/build/three.module.js';
// import { OrbitControls } from 'https://unpkg.com/three/examples/jsm/controls/OrbitControls.js';
// import Stats from "https://unpkg.com/three/examples/jsm/libs/stats.module.js";
// import dat from "https://unpkg.com/three/examples/jsm/libs/dat.gui.module.js";
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';
import Stats from "three/examples/jsm/libs/stats.module.js";
import dat from 'three/examples/jsm/libs/dat.gui.module.js';

export default {
  name: 'Example',
  data() {
    return {
      renderer: null,
      scene: null,
      camera: null,
      cameraControls: null,
      mesh_cube: null,
      mesh_sphere: null,
      mesh_cone: null,
      stats: null,
    }
  },
  methods: {
    init: function() {
      // RENDERER ENGINE
      this.renderer = new THREE.WebGLRenderer({antialias: true});
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(this.renderer.domElement);

      // SCENE
      this.scene = new THREE.Scene();

      // CAMERA
      let fov = 60;
      let aspect = window.innerWidth / window.innerHeight;
      let near = 0.1;
      let far = 10000;
      this.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      this.camera.position.set(0, 0, 8);
      this.cameraControls = new OrbitControls(this.camera, this.renderer.domElement);

      // MODELS
      let geometry = new THREE.BoxGeometry();
      let material = new THREE.MeshBasicMaterial({color: "white", wireframe: true});
      this.mesh_cube = new THREE.Mesh(geometry, material);
      this.mesh_cube.name = "Cube";

      // Sphere
      let geometry_sphere = new THREE.SphereGeometry();
      let material_sphere = new THREE.MeshBasicMaterial({color: "red", wireframe: true});
      this.mesh_sphere = new THREE.Mesh(geometry_sphere, material_sphere);
      this.mesh_sphere.name = "Sphere";

      // Cone
      let geometry_cone = new THREE.ConeGeometry();
      let material_cone = new THREE.MeshBasicMaterial({color: "yellow", wireframe: true});
      this.mesh_cone = new THREE.Mesh(geometry_cone, material_cone);
      this.mesh_cone.name = "Cone";

      // SCENE GRAPH
      this.scene.add(this.mesh_cube);
      this.scene.add(this.mesh_sphere);
      this.scene.add(this.mesh_cone);

      // Initial positions
      this.mesh_cube.position.x = -3;
      this.mesh_sphere.position.x = 0;
      this.mesh_cone.position.x = 3;

      // GUI
      let gui = new dat.GUI();

      // model.cube
      let model = {
          cube: {
              rotY: this.mesh_cube.rotation.y * Math.PI / 180,
              default_params: function(){
                  model.cube.rotY = 0;
                  this.mesh_cube.rotation.y = 0;
                  this.mesh_cube.position.x = -3;
                  this.mesh_cube.material.color = new THREE.Color("white");
                  model.cube.colorPalette = [1,1,1];
                  this.mesh_cube.material.wireframe = true;
              },
              listColors: ["White", "Red", "Yellow"],
              defaultItem: "White",
              colorPalette: [1, 1, 1],
              mesh: this.mesh_cube,
              initial_x: -3
          },
          sphere: {
              rotY: this.mesh_sphere.rotation.y * Math.PI / 180,
              default_params: function(){
                  model.sphere.rotY = 0;
                  this.mesh_sphere.rotation.y = 0;
                  this.mesh_sphere.position.x = 0;
                  this.mesh_sphere.material.color = new THREE.Color("red");
                  model.sphere.colorPalette = [1,1,1];
                  this.mesh_sphere.material.wireframe = true;
              },
              listColors: ["White", "Red", "Yellow"],
              defaultItem: "Red",
              colorPalette: [1, 1, 1],
              mesh: this.mesh_sphere,
              initial_x: 0
          },
          cone: {
              rotY: this.mesh_cone.rotation.y * Math.PI / 180,
              default_params: function(){
                  model.cone.rotY = 0;
                  this.mesh_cone.rotation.y = 0;
                  this.mesh_cone.position.x = 3;
                  this.mesh_cone.material.color = new THREE.Color("yellow");
                  model.cone.colorPalette = [1,1,1];
                  this.mesh_cone.material.wireframe = true;
              },
              listColors: ["White", "Red", "Yellow"],
              defaultItem: "Yellow",
              colorPalette: [1, 1, 1],
              mesh: this.mesh_cone,
              initial_x: 3
          }
      }
      // view & controller
      
      let cubeMenu = gui.addFolder("Cube");
      let sphereMenu = gui.addFolder("Sphere");
      let coneMenu = gui.addFolder("Cone");
      let menus = ['cube', 'sphere', 'cone']

      let object_mapping = {
          'cube': cubeMenu,
          'sphere': sphereMenu,
          'cone': coneMenu
      }

      // Menu items
      for(let i = 0; i < menus.length ; i++){
          let current_shape = menus[i];
          let current_menu = object_mapping[current_shape];
          let current_model = model[current_shape];
          let current_mesh = current_model.mesh;
          
          let txfMeshName = current_menu.add(current_model.mesh, "name").name("Model's Name")
          .onChange(function(event) {
      
          }).onFinishChange(function(event) {
      
          }); 
          // start
          let sliderPosX = current_menu.add(current_mesh.position, "x").min(-5).max(5).step(0.5).setValue(current_model.initial_x).name("X").listen().onChange(function(value) {
      
          });
          let sliderRotY = current_menu.add(current_model, "rotY").min(-180).max(180).step(10).setValue(0).name("Y (deg)").listen().onChange(function(value) {
              current_mesh.rotation.y = current_model.rotY * Math.PI / 180;
          });;
      
          let testWireframe = current_menu.add(current_mesh.material, "wireframe").setValue(true).name("Wireframe").onChange(function(value) {
      
          });
          let testColors = current_menu.add(current_model, "defaultItem", current_model.listColors).name("Color List").onChange(function(item) {
              current_mesh.material.color = new THREE.Color(current_model.defaultItem.toLowerCase());
              current_model.colorPalette = [current_mesh.material.color.r * 255, current_mesh.material.color.g * 255, current_mesh.material.color.b * 255];
          });
          let testPalette = current_menu.addColor(current_model, "colorPalette").name("Color Palette").listen().onChange(function(color) {
              current_mesh.material.color = new THREE.Color(color[0]/255, color[1]/255, color[2]/255);
          });
          let btnRotHome = current_menu.add(current_model, "default_params").name("Reset default values").onChange(function(event) {
              
          });
      }
      // rotMenu.open();
      gui.close()
      
      // STATS
      this.stats = new Stats();
      this.stats.showPanel(0); // FPS
      document.body.appendChild(this.stats.dom);

      // ANIMATION
      this.renderLoop();
    },
    renderLoop(){
        this.stats.begin();
        this.renderer.render(this.scene, this.camera); // DRAW SCENE
        this.updateScene();
        this.stats.end();
        this.stats.update();
        requestAnimationFrame(this.renderLoop);
    },
    updateScene() {
        // this.mesh.rotation.y = this.mesh.rotation.y + 1 * Math.PI / 180;
    }
  },
  mounted() {
    this.init()
  }
}

</script>