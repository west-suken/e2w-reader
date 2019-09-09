<template>
  <div id="app">
    <Camera :on-scanned="onScanned" :is-active="panelState === 'normal'" />
    <CheckedPanel
      v-if="panelState === 'checked'"
      :onChecked="onChecked"
      :currentSeat="currentSeat" />
    <ConfirmPanel
      v-if="panelState === 'confirming'"
      :onApproved="onApproved"
      :onDenied="() => { panelState = 'normal' }"
      :currentSeat="currentSeat" />
    <ErrorPanel
      v-if="panelState === 'error'"
      :onChecked="onChecked" />
  </div>
</template>

<script>
import Camera from '@/components/Camera.vue'
import CheckedPanel from '@/components/CheckedPanel.vue'
import ConfirmPanel from '@/components/ConfirmPanel.vue'
import ErrorPanel from '@/components/ErrorPanel.vue'
import { setTimeout } from 'timers';

export default {
  name: 'app',
  components: {
    Camera,
    CheckedPanel,
    ConfirmPanel,
    ErrorPanel
  },
  data() {
    return {
      approvedSeats: [],
      panelState: 'normal',
      currentSeat: null
    }
  },
  methods: {
    onScanned(data) {
      let seat
      try {
        seat = JSON.parse(data)
      } catch (e) {
        console.error(e)
        this.panelState = 'error'
        return
      }
      this.currentSeat = seat
      if (!seat.type) {
        this.panelState = 'error'
        return
      }
      if (seat.type === 'line') {
        this.onApproved(seat)
        return
      }
      if (seat.type === 'line-student') {
        this.panelState = 'confirming'
      }
    },
    onApproved(seat) {
      this.approvedSeats.push(seat)
      this.panelState = 'checked'
    },
    onChecked() {
      this.panelState = 'normal'
    }
  }
}
</script>

<style>
html, body {
  margin: 0;
  overflow: hidden;
  height: 100%;
}
#app {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: black;
  height: 100%;
}

button {
  margin: 20px;
  border: none;
  background-color: white;
  padding: 10px 30px;
  border-radius: 20px;
}
</style>
