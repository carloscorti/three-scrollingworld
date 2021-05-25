<template>
  <div class="container" ref="world"></div>
</template>

<script>
import * as Three from "three";
import * as dat from "dat.gui";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
// import space from "../assets/space.jpg";

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

      // Scene
      this.scene = new Three.Scene();

      // Texture loader
      const textureLoader = new Three.TextureLoader();

      // Objects
      let geometry = new Three.TorusGeometry(10, 3, 16, 100);

      // Avatar object
      const avatar = new Three.BoxGeometry(3, 3, 3);

      // Moon object
      const moon = new Three.SphereGeometry(3, 32, 32);

      // Avatar texture
      const avatarTexture = textureLoader.load(require("../assets/cube.jpg"));

      // Moon texture
      const moonTexture = textureLoader.load(require("../assets/moon.jpg"));
      const moonNormalTexture = textureLoader.load(
        require("../assets/normal.jpg")
      );

      // Avatar material
      const avatarMaterial = new Three.MeshBasicMaterial({
        map: avatarTexture,
      });

      // Moon material
      const moonMaterial = new Three.MeshStandardMaterial({
        map: moonTexture,
        normalMap: moonNormalTexture,
      });

      // const moonHelper = gui.addFolder("moon");
      // moonHelper
      //   .add(moon.position, "x")
      //   .min(-6)
      //   .max(6)
      //   .step(0.01);
      // moonHelper
      //   .add(moon.position, "y")
      //   .min(-10)
      //   .max(10)
      //   .step(0.01);
      // moonHelper
      //   .add(moon.position, "z")
      //   .min(-30)
      //   .max(30)
      //   .step(0.01);

      // Materials
      let material = new Three.MeshStandardMaterial();
      material.color = new Three.Color(0xff6347);
      material.roughness = 0.2;
      material.metalness = 0.7;

      // Mesh
      this.mesh = new Three.Mesh(geometry, material);
      this.scene.add(this.mesh);

      const avatarMesh = new Three.Mesh(avatar, avatarMaterial);
      this.scene.add(avatarMesh);

      const moonMesh = new Three.Mesh(moon, moonMaterial);
      moonMesh.position.set(-20, 20, 6);
      // moonMesh.position.set(0, 0, 0);
      this.scene.add(moonMesh);

      const moonHelper = gui.addFolder("moon");
      moonHelper
        .add(moonMesh.position, "x")
        .min(-20)
        .max(20)
        .step(0.01);
      moonHelper
        .add(moonMesh.position, "y")
        .min(-20)
        .max(20)
        .step(0.01);
      moonHelper
        .add(moonMesh.position, "z")
        .min(-30)
        .max(30)
        .step(0.01);

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

      // Scene background
      // const spaceTexture = new Three.TextureLoader().load(space);
      const spaceTexture = textureLoader.load(require("../assets/space.jpg"));
      // const spaceTexture = new Three.TextureLoader().load(
      //   "https://images.pexels.com/photos/1205301/pexels-photo-1205301.jpeg"
      // );
      this.scene.background = spaceTexture;
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
