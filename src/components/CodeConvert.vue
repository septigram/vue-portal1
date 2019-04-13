<template>
  <div class="codeConvert">
    <collapse2 title="コード変換">
      <button @click="convUtf8()">平文→URL</button>
      <button @click="convRevUtf8()">URL→平文</button><br/>
      IN:<input v-model='codeInput'/><br/>
      OUT:<input v-model='codeOutput' readonly style="background:#eee;border:1px solid gray;"/>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'codeConvert',
  components: { Collapse2 },
  data: function () {
    return {
      codeInput: '',
      codeOutput: ''
    }
  },
  methods: {
    convUtf8: function () {
      this.codeOutput = encodeURIComponent(this.codeInput)
    },
    convRevUtf8: function () {
      this.codeOutput = decodeURIComponent(this.codeInput)
    }
  }
}
</script>

<style scoped>
</style>
