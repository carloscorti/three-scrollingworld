<template>
    <div class="container" ref="world"></div>
</template>

<script>
import * as Three from 'three'

export default {
  name: 'ThreeComponent',
  data() {
    return {
      camera: null,
      scene: null,
      renderer: null,
      mesh: null
    }
  },
  methods: {
    init: function() {
        let container = this.$refs.world;
        // let container = document.getElementById('container');

        this.camera = new Three.PerspectiveCamera(75, container.clientWidth/container.clientHeight, 0.01, 1000);
        this.camera.position.z = 30;

        this.scene = new Three.Scene();

        // let geometry = new Three.BoxGeometry(0.2, 0.2, 0.2);
        let geometry = new Three.TorusGeometry(10, 3, 16, 100);
        // let material = new Three.MeshNormalMaterial();
        // let material = new Three.MeshBasicMaterial({color: 0xFF6347, wireframe: true});
        let material = new Three.MeshBasicMaterial();
        material.color = new Three.Color(0xFF6347)
        material.wireframe = true

        this.mesh = new Three.Mesh(geometry, material);
        this.scene.add(this.mesh);

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
    }
  },
  mounted() {
      this.init();
      this.animate();
  }
}
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
