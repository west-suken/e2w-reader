<template>
  <div>
    承認されました<br />
    <br />
    座席：{{ seatNumber }}番
    <button @click="onChecked">確認</button>
  </div>
</template>

<script>
export default {
  props: {
    onChecked: Function,
    currentSeat: Object
  },
  computed: {
    seatNumber() {
      if (this.currentSeat && this.currentSeat.seatIndex) {
        return this.currentSeat.seatIndex + 1
      }
      return '読み込めません'
    }
  }
}
</script>

<style scoped>
div {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(26, 188, 156, 0.6);
  font-size: 30px;
  color: white;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
