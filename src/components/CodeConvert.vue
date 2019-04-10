<template>
  <div class="codeConvert">
    <el-collapse v-model="visible">
      <el-collapse-item title="■コード変換" name="c">
        <button @click="convUtf8()">平文→URL</button>
        <button @click="convRevUtf8()">URL→平文</button><br/>
        IN:<input v-model='codeInput'/><br/>
        OUT:<input v-model='codeOutput' readonly style="background:#eee;border:1px solid gray;"/>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'codeConvert',
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
div.codeConvert {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
</style>
