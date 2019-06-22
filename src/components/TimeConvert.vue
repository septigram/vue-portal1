<template>
  <div class="timeConvert">
    <collapse2 title="時間変換">
      <button @click="convTime1()">UNIX時間→時刻</button>
      <button @click="convTime2()">時刻→UNIX時間</button><br/>
      IN:<input v-model='codeInput'/><br/>
      OUT:<input v-model='codeOutput' readonly style="background:#eee;border:1px solid gray;"/>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'timeConvert',
  components: { Collapse2 },
  data: function () {
    return {
      codeInput: '',
      codeOutput: ''
    }
  },
  methods: {
    convTime1: function () {
      this.codeOutput = new Date(this.codeInput - 0).toString()
    },
    convTime2: function () {
      this.codeOutput = new Date(this.codeInput).getTime()
    }
  }
}
</script>

<style scoped>
</style>
