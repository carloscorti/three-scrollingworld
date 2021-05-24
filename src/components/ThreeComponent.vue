<template>
  <div class="container" ref="world"></div>
</template>

<script>
import * as Three from "three";
import * as dat from "dat.gui";

export default {
  name: "ThreeComponent",
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null,
    };
  },
  methods: {
    init: function() {
      let container = this.$refs.world;
      // let container = document.getElementById('container');

      const gui = new dat.GUI();

      this.camera = new Three.PerspectiveCamera(
        75,
        container.clientWidth / container.clientHeight,
        0.01,
        1000
      );
      this.camera.position.z = 30;

      this.scene = new Three.Scene();

      // let geometry = new Three.BoxGeometry(0.2, 0.2, 0.2);
      let geometry = new Three.TorusGeometry(10, 3, 16, 100);
      // let material = new Three.MeshNormalMaterial();
      // let material = new Three.MeshBasicMaterial({color: 0xFF6347, wireframe: true});
      let material = new Three.MeshStandardMaterial();
      material.color = new Three.Color(0xff6347);
      material.roughness = 0.2;
      material.metalness = 0.7;
      // material.wireframe = true

      this.mesh = new Three.Mesh(geometry, material);
      this.scene.add(this.mesh);

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

      // this.renderer = new Three.WebGLRenderer({antialias: true});
      this.renderer = new Three.WebGLRenderer();
      // this.renderer.setPixelRatio(window.devicePixelRatio) ??--??--??
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);
    },
    animate: function() {
      requestAnimationFrame(this.animate);
      this.mesh.rotation.x += 0.01;
      this.mesh.rotation.y += 0.005;
      this.mesh.rotation.z += 0.01;
      this.renderer.render(this.scene, this.camera);
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
