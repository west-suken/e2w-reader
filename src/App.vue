<template>
  <div id="app">
    <Camera />
    hoge
  </div>
</template>

<script>
import Camera from '@/components/Camera.vue'

export default {
  name: 'app',
  components: {
    Camera
  }
}
</script>

<style>
html, body {
  margin: 0;
  overflow: hidden;
}
#app {
}
</style>
