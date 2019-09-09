<template>
  <div>
    名前を言ってください
    <button @click="toggle">{{ label }}</button>
    <section v-if="showName">
      {{ name }}<br />
      <button @click="onApproved">承認</button>
      <button @click="onDenied">拒否</button>
    </section>
  </div>
</template>

<script>
export default {
  props: {
    currentSeat: Object,
    onApproved: Function,
    onDenied: Function
  },
  data() {
    return {
      showName: false
    }
  },
  computed: {
    label() {
      return this.showName ? '非表示' : '表示'
    },
    name() {
      if (this.currentSeat && this.currentSeat.name) {
        return this.currentSeat.name
      } else {
        return ''
      }
    }
  },
  methods: {
    toggle() {
      this.showName = !this.showName
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
  background-color: rgba(241, 196, 15, 0.6);
  font-size: 30px;
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

section {
  text-align: center;
}
</style>
