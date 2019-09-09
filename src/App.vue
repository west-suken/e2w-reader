<template>
  <div id="app">
    <Camera
      :onScanned="onScanned"
      :isActive="panelState === 'normal' || panelState === 'getting-token'" />
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
    <ConnectionPanel
      v-if="panelState === 'connecting'"
      :setPerformance="setPerformance"
      :token="token"
      :onDenied="() => { panelState = 'getting-token' }" />
  </div>
</template>

<script>
import Camera from '@/components/Camera.vue'
import CheckedPanel from '@/components/CheckedPanel.vue'
import ConfirmPanel from '@/components/ConfirmPanel.vue'
import ErrorPanel from '@/components/ErrorPanel.vue'
import ConnectionPanel from '@/components/ConnectionPanel.vue'

export default {
  name: 'app',
  components: {
    Camera,
    CheckedPanel,
    ConfirmPanel,
    ErrorPanel,
    ConnectionPanel
  },
  data() {
    return {
      approvedSeats: [],
      panelState: 'getting-token',
      currentSeat: null,
      performance: {},
      token: ''
    }
  },
  methods: {
    onScanned(data) {
      if (this.panelState === 'getting-token') {
        this.token = data
        this.panelState = 'connecting'
        return
      }
      let seat
      try {
        seat = JSON.parse(data)
      } catch (e) {
        console.error(e)
        this.panelState = 'error'
        return
      }
      this.currentSeat = seat
      if (!seat.type ||
          !this.performance.seats[seat.seatIndex] ||
          this.performance.seats[seat.seatIndex].id !== seat.id ||
          this.performance.seats[seat.seatIndex].type !== seat.type ||
          (this.performance.seats[seat.seatIndex].name &&
           this.performance.seats[seat.seatIndex].name !== seat.name)) {
        this.panelState = 'error'
        return
      }
      if (this.approvedSeats
        .filter(_seat => _seat.seatIndex === seat.seatIndex).length > 0) {
        console.log('already scanned')
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
    },
    setPerformance(performance) {
      this.performance = performance
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
