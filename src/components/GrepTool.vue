<template>
  <div class="grepTool">
    <collapse2 title="GREPツール">
      <input v-model="keyword" @keyup='updateResult()'/>
      <el-checkbox v-model="isRegex" @change='updateResult()'>正規表現</el-checkbox>
      <el-tabs v-model="tab">
        <el-tab-pane label="元データ" name="s">
          {{source.split("\n").length}}
          <el-input type="textarea" :rows='20' v-model="source" placeholder="抽出対象データを貼り付けてください"/>
        </el-tab-pane>
        <el-tab-pane label="抽出結果" name="r">
          {{result.split("\n").length}}
          <el-input type="textarea" :rows='20' v-model="result" :readonly='true'/>
        </el-tab-pane>
      </el-tabs>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'grepTool',
  components: { Collapse2 },
  data: function () {
    return {
      tab: 's',
      isRegex: false,
      keyword: '',
      source: '',
      result: ''
    }
  },
  methods: {
    updateResult: function () {
      if (!this.keyword) {
        return this.source
      }
      const svs = this.source.split('\n')
      const vs = []
      if (this.isRegex) {
        const reg = new RegExp(this.keyword)
        svs.forEach((line) => {
          if (reg.test(line))  {
            vs.push(line)
          }
        })
      } else {
        svs.forEach((line) => {
          if (line.indexOf(this.keyword) >= 0)  {
            vs.push(line)
          }
        })
      }
      this.result = vs.join('\n')
    }
  }
}
</script>

<style scoped>
</style>
