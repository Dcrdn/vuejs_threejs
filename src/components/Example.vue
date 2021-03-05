<template>
  <div id="container"></div>
</template>
<script>
import * as THREE from 'three'
import { OrbitControls } from './jsm/controls/OrbitControls.js';
import Stats from "three/examples/jsm/libs/stats.module.js";
import dat from 'three/examples/jsm/libs/dat.gui.module.js';
// import Stats from "./jsm/libs/stats.module.js";
// import dat from './jsm/libs/dat.gui.module.js';

export default {
  name: 'Example',
  data() {
    return {
      cube: null,
      renderer: null,
      scene: null,
      camera: null,
      mesh: null,
      stats: null,
      cameraControls: null,
    }
  },
  methods: {
    init: function() {
        // Renderer engine
        this.renderer = new THREE.WebGLRenderer({antialias: true});
        this.renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(this.renderer.domElement);

        // Scene
        this.scene = new THREE.Scene();

        // Camera
        let fov = 60;
        let aspect = window.innerWidth / window.innerHeight;
        let near = 0.1;
        let far = 10000;
        this.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
        this.camera.position.set(0, 0, 3);
        this.cameraControls = new OrbitControls(this.camera, this.renderer.domElement);

        // MODELS
        let geometry = new THREE.BoxGeometry();
        let material = new THREE.MeshBasicMaterial({color: "white", wireframe: true});
        this.mesh = new THREE.Mesh(geometry, material);
        this.mesh.name = "Cube";

        // SCENE GRAPH
        this.scene.add(this.mesh);
        this.scene.add(this.mesh);
        // GUI
        let gui = new dat.GUI();

        // model
        let model = {
            rotY: this.mesh.rotation.y * Math.PI / 180,
            posHome: function() {
                this.mesh.position.x = 0;
            },
            rotHome: function() {
                model.rotY = 0;
                this.mesh.rotation.y = 0;
            }
        }

        // General Info Menu
        let generalMenu = gui.addFolder("General Info Menu");
        let txfMeshName = generalMenu.add(this.mesh, "name").name("Model's Name")
        .onChange(function(event) {

        }).onFinishChange(function(event) {

        });

        // Position Menu
        let positionMenu = gui.addFolder("Model's Position Menu");
        let sliderPosX = positionMenu.add(this.mesh.position, "x").min(-5).max(5).step(0.5).setValue(0).name("X").listen().onChange(function(value) {
        
        });
        let btnPosHome = positionMenu.add(model, "posHome").name("HOME").onChange(function(event) {

        });
        
        // Orientation Menu
        let rotMenu = gui.addFolder("Model's Rotation Menu");
        let sliderRotY = rotMenu.add(model, "rotY").min(-180).max(180).step(10).setValue(0).name("Y (deg)").listen().onChange(function(value) {
            this.mesh.rotation.y = model.rotY * Math.PI / 180;
        });;

        let btnRotHome = rotMenu.add(model, "rotHome").name("HOME").onChange(function(event) {

        });

        // Appearance Menu
        let appearMenu = gui.addFolder("Model's Appearance Menu");
        let chbWireframe = appearMenu.add(this.mesh.material, "wireframe").setValue(true).name("Wireframe").onChange(function(value) {

        });

        var params = {
            modelcolor: "#ff0000",
            direction: "Derecha",
            forma: "Cubo"
        };

        // Color Menu
        let colorMenu = gui.addFolder("Color del cubo");

        let chColor = colorMenu.addColor(params, 'modelcolor').name('Model Color').onChange(function() {
                // this.mesh.material.color.set = "red"
                console.log(this.mesh.material.color);
                // this.mesh.color.set(params.modelcolor)
                // materialmodel.color.set(params.modelcolor);
        });

        // Movimiento
        let movimientoMenu = gui.addFolder("Movimiento del cubo");
        let opMovimiento = movimientoMenu.add(params, 'direction', ['Derecha', 'Izquierda']).name("Dirección").onChange(function(value) {
            alert(value);   
            this.mesh.rotation.y = this.mesh.rotation.y
        });

        // Forma
        let formaMenu = gui.addFolder("Forma del cubo");
        let opForma = formaMenu.add(params, 'forma', ['Cubo', 'Esfera', 'Piramide']).name("Forma").onChange(function(value) {

        });


        rotMenu.open();
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
        this.mesh.rotation.y = this.mesh.rotation.y + 1 * Math.PI / 180;
    }
  },
  mounted() {
    this.init()
  }
}

</script>