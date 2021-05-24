<template>
  <div id="app">
    <ThreeComponent/>
  </div>
</template>

<script>
import ThreeComponent from './components/ThreeComponent.vue'

export default {
  name: 'App',
  components: {
    ThreeComponent
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}
</style>
