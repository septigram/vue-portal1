<template>
  <div class="sudoku">
    <collapse2 title="Sudoku">
      <div style="float:right;display:inline-block;font-size:small;">
        {{timer}}
      </div>
      <el-menu mode="horizontal" @select='onmenu'>
        <el-submenu index="file">
          <template slot="title">Game</template>
          <el-menu-item index="new">New</el-menu-item>
          <el-menu-item index="reset">Reset</el-menu-item>
          <el-menu-item index="clear">Clear</el-menu-item>
          <el-submenu index="mode">
            <template slot="title">Mode</template>
            <el-menu-item index="easy"><i v-if='mode==="easy"' class="el-icon-check"></i>Easy</el-menu-item>
            <el-menu-item index="normal"><i v-if='mode==="normal"' class="el-icon-check"></i>Normal</el-menu-item>
            <el-menu-item index="hard"><i v-if='mode==="hard"' class="el-icon-check"></i>Hard</el-menu-item>
          </el-submenu>
          <el-submenu index="hint">
            <template slot="title">Hint</template>
            <el-menu-item index="guide"><i v-if='showGuide' class="el-icon-check"></i>Show guide</el-menu-item>
          </el-submenu>
        </el-submenu>
      </el-menu>
      <transition name='slide'>
        <div v-if='end' class="goal">
          <div class="goalMessage">
            Complete!
          </div>
          <div class="goalElments">
            Time: {{goalTime}}<br/>
            <el-button @click='clear'>OK</el-button>
          </div>
        </div>
      </transition>
      <table class="board">
        <tr v-for='(r,ri) in board.rows' :key='ri'>
          <td v-for='(c,ci) in r.cols' :key='ci' @click='onclick(c)' :class='{error:c.error,b0:c.blockPos===0,b1:c.blockPos===1,b2:c.blockPos===2,b3:c.blockPos===3,b4:c.blockPos===4,b5:c.blockPos===5,b6:c.blockPos===6,b7:c.blockPos===7,b8:c.blockPos===8}'>
            <template v-if='c.value'>
              <div :class='{fixed: c.fixed}'>{{c.value}}</div>
            </template>
            <template v-else>
              <table class="guide">
                <tr v-for='(gr,gri) in guides' :key='gri'>
                  <td v-for='(gc,gci) in gr' :key='gci' :class='{selected:gc.value===selectNum}'>
                    <template v-if='showGuide&&c.avails.indexOf(gc.value)>=0'>{{gc.value}}</template>
                    <template v-else>&nbsp;</template>
                  </td>
                </tr>
              </table>
            </template>
          </td>
        </tr>
        <tr>
          <td v-for='(b,bi) in buttons' :key='bi' :class='{button:true, selected: b.selected, end:b.end}' @click='select(b)'>
            <div>{{b.value}}</div>
          </td>
        </tr>
      </table>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'sudoku',
  components: { Collapse2 },
  data: function () {
    return {
      board: {
        // 固定, 空, 数値
        rows: []
      },
      stage: -1,
      mode: 'easy',
      beginTime: 0,
      timer: '00:00:00',
      goalTime: '',
      buttons: [],
      guides: [],
      selectNum: 1,
      showGuide: true,
      samples: [
        '724596381580000064901080502800020006309618705600050003406030107270000039135967248',
        '068192340729000158010785020805209604604010209102603805040327080287000463051864790',
        '021060840483521976790000051010845090074139520050276030230000065165394782047050310',
        '800354001054912870021070540540000019789020435210000068095060380078235190100498007',
        '070189040408000107190746058807365409604971803309428705980634071506000902030592080',
        '091080560063040890800000001000876000054109780000524000100000004037010650045090120',
        '800907006050103020902000801016839270000615000098742610105000403040301060700406002',
        '500978004900000007042000890008307600005010300009204700014000980300000002800642003',
        '097000120002179300000205000970804015080010060310506097000702000003451900021000570',
        '000000000680157049940060071020706080890010063070508010210040097430271056000000000',
        '890000075407000309032000640000376000000819000000524000064000290709000804580000063',
        '001050300860000042570000091000628000002513700000947000410000087350000024006080100',
        '004386900000090000901000302700030006380612045200040003403000108000070000008423500',
        '000090000052000860081030970000486000609517402000329000098060130027000690000070000',
        '000805000500000008980704031405309806000010000809607205270106094100000002000402000',
        '076020130005000200100803009200658007007431900800297005700305001002000800061080590',
        '700000008000137000006258400360020014520804073410070029005786300000591000600000005',
        '200000001003020600091653840008579100057102480002864900036947210009010300100000009',
        '800000001097060450021070680000439000039215860000786000082040530046050210100000008',
        '001000200580701049640090071030605090200010004060304010410050063850106027006000100',
        '001060900600308005450000061010050090004831500080020030130000057500903002007040300',
        '300090001004501700091000680030207010800000005010904020053000260008705100100030009',
        '260000053053070420004030600000307000480020065000405000005040800078010590640000032',
        '070040030304000905090050060000587000408231706000496000040060020507000308020070050',
        '605000107080070050003905200300807004040010070500409008009308400060040090401000805'
      ],
      sample1: [
        [7, 2, 4, 5, 9, 6, 3, 8, 0],
        [5, 8, 3, 2, 7, 1, 9, 6, 4],
        [9, 6, 1, 4, 8, 3, 5, 7, 2],
        [8, 5, 7, 3, 2, 9, 4, 1, 6],
        [3, 4, 9, 6, 1, 8, 7, 2, 5],
        [6, 1, 2, 7, 5, 4, 8, 9, 3],
        [4, 9, 6, 8, 3, 2, 1, 5, 7],
        [2, 7, 8, 1, 4, 5, 6, 3, 9],
        [1, 3, 5, 9, 6, 7, 2, 4, 8],
      ],
      end: false
    }
  },
  mounted: function () {
    for (let r = 0; r < 9; r++) {
      const cols = []
      for (let c = 0; c < 9; c++) {
        const value = 0
        cols.push({
          row: r,
          col: c,
          block: Math.floor(r / 3) * 3 + Math.floor(c / 3),
          blockPos: (r % 3) * 3 + (c % 3),
          value: value,
          fixed: value != 0,
          avails: [],
          error: false
        })
      }
      this.board.rows.push({
        row: r,
        cols: cols
      })
      this.buttons.push({
        button: r,
        selected: r === 0,
        end: false,
        value: r + 1
      })
    }
    for (let r = 0; r < 3; r++) {
      const cols = []
      for (let c = 0; c < 3; c++) {
        cols.push({
          value: r * 3 + c + 1
        })
      }
      this.guides.push(cols)
    }
    //this.reset()
    setInterval(()=>{this.tick()},1000)
  },
  methods: {
    tick: function () {
      if (this.beginTime === 0) {
        this.timer = '0:00:00'
        return
      }
      const now = new Date()
      const diff = Math.floor((now.getTime() - this.beginTime) / 1000)
      const h = Math.floor(diff / 3600)
      const m = Math.floor(diff / 60) % 60
      const s = diff % 60
      this.timer = h + ':' + (m < 10 ? '0' : '') + m + ':' + (s < 10 ? '0' : '') + s
    },
    new: function () {
      const o = this.mode === 'easy' ? 0 : (this.mode === 'normal' ? 7 : 15)
      this.stage = o + Math.floor(Math.random() * 10)
      this.reset()
    },
    clear: function () {
      this.stage = -1
      this.showGuide = false
      this.reset()
    },
    reset: function () {
      for (let r = 0; r < 9; r++) {
        for (let c = 0; c < 9; c++) {
          const v = this.stage === -1 ? 0 : this.samples[this.stage].charAt(r * 9 + c) - 0
          this.board.rows[r].cols[c].value = v
          this.board.rows[r].cols[c].fixed = v != 0
        }
      }
      this.beginTime = this.stage === -1 ? 0 : new Date().getTime()
      this.timer = '0:00:00'
      this.check()
    },
    select: function (button) {
      this.buttons.forEach((b) => {b.selected = button === b})
      this.selectNum = button.value
    },
    onclick: function (cell) {
      if (cell.fixed || this.end) {
        return
      }
      if (cell.value != this.selectNum) {
        cell.value = this.selectNum
      } else {
        cell.value = 0
      }
      this.check()
    },
    check: function () {
      // カウンタ初期化
      const counter = []
      for (let t = 0; t < 3; t++) {
        counter[t] = []
        for (let u = 0; u < 9; u++) {
          counter[t][u] = []
          for (let v = 0; v < 10; v++) {
            counter[t][u][v] = 0
          }
        }
      }
      // 全ブロックを数え上げる
      this.board.rows.forEach((row) => {
        row.cols.forEach((cell) => {
          counter[0][cell.row][cell.value]++
          counter[1][cell.col][cell.value]++
          counter[2][cell.block][cell.value]++
        })
      })
      // ブロックごと：欠落している数字は何か、重複している数字は何か
      // セルごとに：所属するブロックすべてに欠落している数字が候補
      this.board.rows.forEach((row) => {
        row.cols.forEach((cell) => {
          cell.error = counter[0][cell.row][cell.value] > 1 ||
            counter[1][cell.col][cell.value] > 1 ||
            counter[2][cell.block][cell.value] > 1
          cell.avails.splice(0, cell.avails.length)
          for (let n = 1; n <= 9; n++) {
            if (counter[0][cell.row][n] === 0 &&
              counter[1][cell.col][n] === 0 &&
              counter[2][cell.block][n] === 0) {
                cell.avails.push(n)
            }
          }
        })
      })
      // 揃っている数字を調査
      let allok = 0
      for (let v = 1; v < 10; v++) {
        let ok = 0
        for (let t = 0; t < 3; t++) {
          for (let u = 0; u < 9; u++) {
            if (counter[t][u][v] === 1) {
              ok++
            }
          }
        }
        this.buttons[v - 1].end = ok === 27
        if (ok === 27) {
          allok++
        }
      }
      this.end = allok === 9
      if (this.end && this.beginTime) {
        this.goalTime = this.timer
        this.beginTime = 0
      }
    },
    onmenu: function (i) {
      if (i === 'new') {
        this.new()
      }
      if (i === 'reset') {
        this.reset()
      }
      if (i === 'easy' || i === 'normal' || i === 'hard') {
        this.mode = i
      }
      if (i === 'guide') {
        this.showGuide = !this.showGuide
      }
      if (i === 'clear') {
        this.clear()
      }
    }
  }
}
</script>

<style scoped>
table, td {
  border-collapse: collapse;
  text-align: center;
  margin: 0;
  padding: 0;
}
table.board>tr>td {
  border: 1px solid silver;
  cursor: pointer;
  width: 2em;
  text-align: center;
}
table.board>tr>td.error {
  color: red;
}
table.board>tr>td.b0 {
  border-top-color: black;
  border-left-color: black;
}
table.board>tr>td.b1 {
  border-top-color: black;
}
table.board>tr>td.b2 {
  border-top-color: black;
  border-right-color: black;
}
table.board>tr>td.b3 {
  border-left-color: black;
}
table.board>tr>td.b5 {
  border-right-color: black;
}
table.board>tr>td.b6 {
  border-bottom-color: black;
  border-left-color: black;
}
table.board>tr>td.b7 {
  border-bottom-color: black;
}
table.board>tr>td.b8 {
  border-bottom-color: black;
  border-right-color: black;
}
table.board>tr>td>div.fixed {
  color: #999;
  font-weight: bold;
  text-shadow: 1px 1px 1px #000;
  background: #eee;
  height: 2em;
  cursor: default;
}
table.guide {
  margin: auto;
}
table.guide>tr>td {
  color: #9c9;
  line-height: 1em;
  font-size: 0.3em;
  width: 0.3em;
}
table.guide>tr>td.selected {
  color: #060;
}
td.button>div {
  cursor: pointer;
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  border: 1px outset #ccc;
  background:#ccc;
  color: white;
  text-shadow: 0 0 1px #000;
  font-weight: bold;
}
td.button.selected>div {
  border: 1px inset #ccc;
  background:#999;
}
td.button.end>div {
  color: green;
}
div.goal {
  position: relative;
  height: 0;
}
div.goalElments {
  position: relative;
  top: 7em;
  text-align: center;
  width: calc(100% - 1em);
  box-sizing: border-box;
  border-radius: 0.5em;
  color: #fff;
  padding: 1em;
  margin: 0.5em;
  text-shadow: 0px 0px 4px #000;
  background: rgba(0,0,0,0.5);
  font-weight: bold;
}

div.goalMessage {
  position: relative;
  top: 2em;
  left: -0.2em;
  font-family: 'Lobster';
  font-size: 3em;
  transform: rotate(-10deg);
  text-align: center;
  font-weight: bold;
  color: #3f9;
  text-shadow: 4px 4px 4px #000;
}

.slide-enter-active, .slide-leave-active {
  transition: opacity .5s;
}
.slide-enter, .slide-leave-to {
  opacity: 0;
}

</style>
