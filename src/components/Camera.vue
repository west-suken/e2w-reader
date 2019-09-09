<template>
  <canvas ref="canvas"></canvas>
</template>

<script>
import jsQR from 'jsqr'

const video = document.createElement('video')

export default {
  props: {
    onScanned: Function,
    isActive: Boolean
  },
  data() {
    return {
      ctx: null
    }
  },
  mounted() {
    this.ctx = this.$refs.canvas.getContext('2d')
    navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } }).then(stream => {
      video.srcObject = stream;
      video.setAttribute("playsinline", true); // required to tell iOS safari we don't want fullscreen
      video.play();
      requestAnimationFrame(this.tick);
    });
  },
  methods: {
    tick() {
      if (this.isActive) {
        const canvas = this.$refs.canvas
        canvas.width = video.videoWidth
        canvas.height = video.videoHeight
        this.ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
        if (canvas.width > 0) {
          const imageData = this.ctx.getImageData(0, 0, canvas.width, canvas.height);
          const code = jsQR(imageData.data, imageData.width, imageData.height, {
            inversionAttempts: "dontInvert",
          });
          if (code !== null) {
            this.onScanned(code.data)
          }
        }
      }
      requestAnimationFrame(this.tick);
    }
  }
}
</script>
