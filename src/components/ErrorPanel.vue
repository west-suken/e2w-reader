<template>
  <div>
    無効なQRコードです
    <button @click="onChecked">確認</button>
  </div>
</template>

<script>
export default {
  props: {
    onChecked: Function,
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
  background-color: rgba(231, 76, 60, 0.6);
  font-size: 30px;
  color: white;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
</style>
