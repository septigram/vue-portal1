<template>
  <div class="jsonTool">
    <el-collapse v-model='showTools'>
      <el-collapse-item name="j">
        <template slot="title">
          ■JSONツール
        </template>
        ■整形前<br/>
        <el-input type="textarea" :rows='20' v-model='baseJson'/><br/>
        <el-button @click='jsonExpand()'><fa icon="arrow-down"/>展開</el-button>
        <el-button @click='jsonComplex()'><fa icon="arrow-up"/>縮小</el-button>
        <el-button @click='jsonBaseClear()'>↑クリア</el-button>
        <el-button @click='jsonFmtClear()'>↓クリア</el-button>
        <el-checkbox v-model='trimObj'>trim</el-checkbox><br/>
        ■整形後<br/>
        <el-input type="textarea" :rows='20' v-model='fmtJson'/><br/>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'jsonTool',
  data: function () {
    return {
      showTools: [],
      baseJson: '',
      fmtJson: '',
      trimObj: true
    }
  },
  methods: {
    jsonExpand: function () {
      const obj = JSON.parse(this.baseJson)
      this.fmtJson = this.convJson(obj)
    },
    jsonComplex: function () {
      const obj = JSON.parse(this.fmtJson)
      this.baseJson = JSON.stringify(obj)
    },
    jsonBaseClear: function () {
      this.baseJson = ''
    },
    jsonFmtClear: function () {
      this.fmtJson = ''
    },
    convJson: function (obj) {
      let v = JSON.stringify(obj, undefined, '\t')
      if (this.trimObj) {
        const lines = v.split('\n')
        const vs = []
        let ins = []
        let inObj = false
        for (let i = 0; i < lines.length; i++) {
          const line = lines[i]
          const token = line.trim()
          if (token.match(/".*": [^,]*,?[^{[]/)) {
            ins.push({ line: line, token: token })
          } else if (token == '{') {
            ins.push({ line: line, token: line })
            inObj = true
          } else if (token == '}' || token == '},') {
            ins.push({ line: line, token: token })
            if (inObj) {
              const vs2 = []
              ins.forEach((s) => vs2.push(s.token))
              vs.push(vs2.join(' '))
            } else {
              ins.forEach((s) => vs.push(s.line))
            }
            ins = []
            inObj = false
          } else {
            ins.forEach((s) => vs.push(s.line))
            ins = []
            vs.push(line)
          }
        }
        v = vs.join('\n')
      }
      return v
    }
  }
}
</script>

<style scoped>
div.jsonTool {
  border: 1px solid gray;
  border-radius: 0.5em;
  padding: 0.1em;
  margin: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
</style>