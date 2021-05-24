<template>
  <div class="container" ref="world"></div>
</template>

<script>
import * as Three from "three";
import * as dat from "dat.gui";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";

export default {
  name: "ThreeComponent",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
      orbitControl: null,
    };
  },
  methods: {
    init: function() {
      let container = this.$refs.world;
      // let container = document.getElementById('container');

      // Debug
      const gui = new dat.GUI();

      /**
       * Camera
       */
      // Base camera
      this.camera = new Three.PerspectiveCamera(
        75,
        container.clientWidth / container.clientHeight,
        0.01,
        1000
      );
      this.camera.position.z = 30;

      this.scene = new Three.Scene();

      // Objects
      let geometry = new Three.TorusGeometry(10, 3, 16, 100);

      // Materials
      let material = new Three.MeshStandardMaterial();
      material.color = new Three.Color(0xff6347);
      material.roughness = 0.2;
      material.metalness = 0.7;

      // Mesh
      this.mesh = new Three.Mesh(geometry, material);
      this.scene.add(this.mesh);

      // Grid helper
      const gridHelper = new Three.GridHelper(200, 50);
      this.scene.add(gridHelper);

      // Lights
      const ambientLight = new Three.AmbientLight(0xffffff, 1);
      this.scene.add(ambientLight);

      const pointLight = new Three.PointLight(0xffffff, 4);
      pointLight.position.x = 2;
      pointLight.position.y = 3;
      pointLight.position.z = 8;
      this.scene.add(pointLight);

      const pointLightHelper = new Three.PointLightHelper(pointLight, 1);
      this.scene.add(pointLightHelper);

      const light = gui.addFolder("light");
      light
        .add(pointLight.position, "x")
        .min(-6)
        .max(6)
        .step(0.01);
      light
        .add(pointLight.position, "y")
        .min(-10)
        .max(10)
        .step(0.01);
      light
        .add(pointLight.position, "z")
        .min(-30)
        .max(30)
        .step(0.01);
      light
        .add(pointLight, "intensity")
        .min(0)
        .max(10)
        .step(0.01);
      // gui
      //   .add(pointLight.position, "x")
      //   .min(-6)
      //   .max(6)
      //   .step(0.01);
      // gui
      //   .add(pointLight.position, "y")
      //   .min(-10)
      //   .max(10)
      //   .step(0.01);
      // gui
      //   .add(pointLight.position, "z")
      //   .min(-30)
      //   .max(30)
      //   .step(0.01);
      // gui
      //   .add(pointLight, "intensity")
      //   .min(0)
      //   .max(10)
      //   .step(0.01);

      /**
       * Renderer
       */
      // this.renderer = new Three.WebGLRenderer({antialias: true});
      this.renderer = new Three.WebGLRenderer();
      // this.renderer.setPixelRatio(window.devicePixelRatio) ??--??--??
      this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);

      // Orbit control
      this.orbitControl = new OrbitControls(
        this.camera,
        this.renderer.domElement
      );

      Array(200)
        .fill()
        .forEach(this.addStar);
    },

    /**
     * Animate
     */
    animate: function() {
      requestAnimationFrame(this.animate);
      this.mesh.rotation.x += 0.01;
      this.mesh.rotation.y += 0.005;
      this.mesh.rotation.z += 0.01;

      this.orbitControl.update();
      this.renderer.render(this.scene, this.camera);
    },

    addStar: function() {
      const geometry = new Three.SphereGeometry(0.25, 24, 24);
      const material = new Three.MeshStandardMaterial({ color: 0xffffff });
      const star = new Three.Mesh(geometry, material);

      const [x, y, z] = Array(3)
        .fill()
        .map(() => Three.MathUtils.randFloatSpread(100));

      star.position.set(x, y, z);

      this.scene.add(star);
    },
  },
  mounted() {
    this.init();
    this.animate();
  },
};
</script>

<style lang="scss">
.container {
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}
</style>
