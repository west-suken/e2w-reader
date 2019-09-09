<template>
  <canvas ref="canvas"></canvas>
</template>

<script>
import jsQR from 'jsqr'

const video = document.createElement('video')

export default {
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
    window.addEventListener('resize', () => {
      this.$refs.canvas.width = window.innerWidth
      this.$refs.canvas.height = window.innerHeight
    })
    this.$refs.canvas.width = window.innerWidth
    this.$refs.canvas.height = window.innerHeight
  },
  methods: {
    tick() {
      const canvas = this.$refs.canvas
      const rate = Math.max(canvas.width / video.videoWidth, canvas.height / video.videoHeight)
      let videoWidth = video.videoHeight * rate //video.videoWidth / videoHeight * canvas.height
      let videoHeight = video.videoHeight * rate
      // if (videoWidth < canvas.width) {
      //   videoWidth = canvas.width
      //   videoHeight = video.videoHeight / videoWidth * canvas.width
      // }
      this.ctx.drawImage(video, 0, 0, videoWidth, videoHeight);
      if (canvas.width > 0) {
        const imageData = this.ctx.getImageData(0, 0, canvas.width, canvas.height);
        const code = jsQR(imageData.data, imageData.width, imageData.height, {
          inversionAttempts: "dontInvert",
        });
        console.log(code)
      }
      requestAnimationFrame(this.tick);
    }
  }
}
</script>
