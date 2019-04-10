<template>
  <div class="timeConvert">
    <el-collapse v-model="visible">
      <el-collapse-item title="■時間変換" name="c">
        <button @click="convTime1()">UNIX時間→時刻</button>
        <button @click="convTime2()">時刻→UNIX時間</button><br/>
        IN:<input v-model='codeInput'/><br/>
        OUT:<input v-model='codeOutput' readonly style="background:#eee;border:1px solid gray;"/>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'timeConvert',
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
div.timeConvert {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
</style>
