<template>
  <div>
    <section v-if="status === 'connecting'">
      通信中です
    </section>
    <section v-if="status === 'failed'">
      正しいトークンではありません<br />
      <button @click="retry">撮り直し</button>
    </section>
    <section v-if="status === 'selecting'">
      公演を選んでください<br />
      <button v-for="title in titleList" @click="select(title)">
        {{ title }}
      </button>
    </section>
  </div>
</template>

<script>
export default {
  props: {
    token: String,
    onDenied: Function,
    setPerformance: Function
  },
  data() {
    return {
      status: 'connecting',
      performanceList: {}
    }
  },
  mounted() {
    const xhr = new XMLHttpRequest()
    xhr.open('POST', 'https://asia-northeast1-easy-to-wait.cloudfunctions.net/getPerformanceList')
    xhr.onload = () => {
      console.log(xhr.response)
      if (xhr.response.substring(0, 1) !== '{') {
        this.status = 'failed'
        return
      }
      this.performanceList = JSON.parse(xhr.response)
      this.status = 'selecting'
    }
    xhr.send(JSON.stringify({
      collectionName: 'psychopath',
      token: this.token
    }))
  },
  methods: {
    retry() {
      this.status = 'connecting'
      this.onDenied()
    },
    select(title) {
      this.setPerformance(this.performanceList[title])
    }
  },
  computed: {
    titleList() {
      return Object.keys(this.performanceList)
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
  background-color: rgba(155, 89, 192, 0.8);
  font-size: 30px;
  color: white;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
section {
  text-align: center;
}
</style>
