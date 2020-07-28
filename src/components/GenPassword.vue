<template>
  <div class="genpass">
    <collapse2 title="パスワード生成">
      <el-input v-model='passwd' style="width: 24em;"/>
      <br/>
      <div class="a">
        <el-checkbox v-model='alphaUpperFlag' :disabled="distinctivenessFlag">A</el-checkbox>
      </div>
      <div class="a">
        <el-checkbox v-model='alphaLowerFlag' :disabled="distinctivenessFlag">a</el-checkbox>
      </div>
      <div class="a">
        <el-checkbox v-model='numbersFlag' :disabled="distinctivenessFlag">1</el-checkbox>
      </div>
      <div class="a">
        <el-checkbox v-model='symbolsFlag' :disabled="distinctivenessFlag">@</el-checkbox>
      </div>
      <br/>
      <div class="b">
        <el-checkbox v-model='distinctivenessFlag'>高識別性</el-checkbox>
      </div>
      長さ：
      <div class="a">
        <el-input v-model='passwdLength' max-length="3" size="mini"/>
      </div>
      <div class="a">
        <el-button @click='genPasswd' :autosize='true' size="mini" type="primary">生成</el-button>
      </div>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'genpass',
  components: { Collapse2 },
  data: function () {
    return {
      passwd: '',
      alphaLower: [ 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z' ],
      alphaUpper: [ 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z' ],
      numbers: [ '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' ],
      symbols: [ '!', '"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/', ':', ';', '<', '=', '>', '?', '@', '[', '\\', ']', '^', '_', '`', '{', '|', '}', '~' ],
      readables: [ '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'P', 'A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', 'X', 'C', 'V', 'B', 'N', 'M' ],
      alphaLowerFlag: true,
      alphaUpperFlag: true,
      numbersFlag: true,
      symbolsFlag: false,
      distinctivenessFlag: false,
      passwdLength: 16
    }
  },
  methods: {
    genPasswd: function () {
      this.passwdLength -= 0
      if (isNaN(this.passwdLength) || this.passwdLength < 0 || this.passwdLength > 1000) {
        this.passwdLength = 8
      }
      const vs = []
      let candidates = []
      if (this.distinctivenessFlag) {
        candidates = candidates.concat(this.readables)
        vs.push(this.getRandomChar(this.readables))
      } else {
        if (this.alphaLowerFlag) {
          candidates = candidates.concat(this.alphaLower)
          vs.push(this.getRandomChar(this.alphaLower))
        }
        if (this.alphaUpperFlag) {
          candidates = candidates.concat(this.alphaUpper)
          vs.push(this.getRandomChar(this.alphaUpper))
        }
        if (this.numbersFlag) {
          candidates = candidates.concat(this.numbers)
          vs.push(this.getRandomChar(this.numbers))
        }
        if (this.symbolsFlag) {
          candidates = candidates.concat(this.symbols)
          vs.push(this.getRandomChar(this.symbols))
        }
      }
      if (vs.length > this.passwdLength) {
        vs.splice(0, vs.length - this.passwdLength)
      }
      for (let i = vs.length; i < this.passwdLength; i++) {
        vs.push(this.getRandomChar(candidates))
      }
      for (let i = 0; i < vs.length; i++) {
        const r = Math.floor(Math.random() * vs.length)
        const t = vs[i]
        vs[i] = vs[r]
        vs[r] = t
      }
      this.passwd = vs.join('')
    },
    getRandomChar: function (vs) {
      return vs[Math.floor(Math.random() * vs.length)]
    }
  }
}
</script>

<style scoped>
div.a {
  display: inline-block;
  width: 4em;
}
div.b {
  display: inline-block;
}
</style>
