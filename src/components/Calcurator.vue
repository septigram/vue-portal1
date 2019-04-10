<template>
  <div class="calculator">
    ■電卓<br/>
    <div class="disp">
      {{dispValue}}
    </div>
    <el-button @click='input(7)' size="mini">7</el-button>
    <el-button @click='input(8)' size="mini">8</el-button>
    <el-button @click='input(9)' size="mini">9</el-button>
    <el-button @click='input("*")' size="mini">*</el-button>
    <el-button @click='input("C")' size="mini">C</el-button>
    <br/>
    <el-button @click='input(4)' size="mini">4</el-button>
    <el-button @click='input(5)' size="mini">5</el-button>
    <el-button @click='input(6)' size="mini">6</el-button>
    <el-button @click='input("/")' size="mini">/</el-button>
    <el-button @click='input("BS")' size="mini">BS</el-button>
    <br/>
    <el-button @click='input(1)' size="mini">1</el-button>
    <el-button @click='input(2)' size="mini">2</el-button>
    <el-button @click='input(3)' size="mini">3</el-button>
    <el-button @click='input("-")' size="mini">-</el-button>
    <br/>
    <el-button @click='input(0)' size="mini">0</el-button>
    <el-button @click='input("00")' size="mini">00</el-button>
    <el-button @click='input(".")' size="mini">.</el-button>
    <el-button @click='input("+")' size="mini">+</el-button>
    <el-button @click='input("=")' size="mini">=</el-button>
    <br/>
  </div>
</template>

<script>
export default {
  name: 'calculator',
  data: function () {
    return {
      inputValue: '0',
      fraction: 0,
      denominator: 1,
      operator: null,
      nextClear: false
    }
  },
  computed: {
    dispValue: function () {
      if (this.inputValue) {
        return this.inputValue
      }
      let v = (this.fraction / this.denominator) + ''
      const dot = v.indexOf('.')
      if (dot >= 0) {
        if (v.length > 12) {
          v = v.substring(0, 12)
        }
      }
      return v
    }
  },
  methods: {
    input: function (v) {
      switch (v) {
      case 1:
      case 2:
      case 3:
      case 4:
      case 5:
      case 6:
      case 7:
      case 8:
      case 9:
        if (this.inputValue === null || this.inputValue === '0') {
          this.inputValue = ''
        }
        this.inputValue += v + ''
        break
      case 0:
      case "00":
        if (this.inputValue !== '0') {
          if (this.inputValue === null) {
            this.inputValue = ''
          }
          this.inputValue += v + ''
        }
        break
      case '.':
        if (this.inputValue === null) {
          this.inputValue = ''
        }
        this.inputValue += v + ''
        break
      case '*':
      case '/':
      case '+':
      case '-':
        if (this.operator === null) {
          this.operator = '+'
        }
        this.calc()
        this.operator = v
        break
      case '=':
        this.calc()
        this.operator = null
        break
      case 'C':
        this.inputValue = '0'
        this.fraction = 0
        this.denominator = 1
        this.operator = null
        break
      case 'BS':
        if (this.inputValue.length > 1) {
          this.inputValue = this.inputValue.substring(0, this.inputValue.length - 1)
        } else {
          this.inputValue = '0'
        }
        break
      }
    },
    calc: function () {
      if (this.operator && this.inputValue) {
        const v2 = parseFloat(this.inputValue)
        switch (this.operator) {
        case '*':
          this.fraction *= v2
          break;
        case '/':
          this.denominator *= v2
          break;
        case '+':
          this.fraction += this.denominator * v2
          break;
        case '-':
          this.fraction -= this.denominator * v2
          break;
        }
        this.operator = null
        this.inputValue = null
      }
    }
  }
}
</script>

<style scoped>
div.calculator {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
div.disp {
  border: 1px solid gray;
  border-radius: 0.2em;
  margin: 0.2em;
  padding: 0.3em;
  font-family: monospace;
  text-align: right;
  font-size: x-large;
  font-weight: bold;
}
button.el-button {
  padding: 0;
  margin: 0.1em;
  width: 1.5em;
  height: 1.2em;
  font-size: large;
}
</style>
