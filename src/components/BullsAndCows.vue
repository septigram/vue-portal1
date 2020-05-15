<template>
  <div class="bullsAndCows">
    <collapse2 title="BullsAndCows">
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
      <div :class="{end}">
        {{timer}}
        <el-button @click="newGame" size="mini">New game</el-button>
        <a href="https://en.wikipedia.org/wiki/Bulls_and_Cows" target="_blank">rule</a><br/>
        <div class="c c0" @click="append(0)">0</div>
        <div class="c c1" @click="append(1)">1</div>
        <div class="c c2" @click="append(2)">2</div>
        <div class="c c3" @click="append(3)">3</div>
        <div class="c c4" @click="append(4)">4</div><br/>
        <div class="c c5" @click="append(5)">5</div>
        <div class="c c6" @click="append(6)">6</div>
        <div class="c c7" @click="append(7)">7</div>
        <div class="c c8" @click="append(8)">8</div>
        <div class="c c9" @click="append(9)">9</div><br/>
        <div class="cmd cc" @click="cls()">Clear</div>
        <div class="cmd cv" @click="check()"><i class="el-icon-check"></i>Check</div>
        turn:{{turn}}
        p:{{p}}
        <template v-for='(b, bi) in board'>
          <div :key='bi' :class="{ turn: true, current: turn === bi, cleard: bi < turn }">
            {{b.turn}}
            <template v-for='(n, ni) in b.state'>
              <div :key='ni' :class="{
                c: true,
                c0: n === 0,
                c1: n === 1,
                c2: n === 2,
                c3: n === 3,
                c4: n === 4,
                c5: n === 5,
                c6: n === 6,
                c7: n === 7,
                c8: n === 8,
                c9: n === 9,
              }">{{n !== '' ? n : '&nbsp;'}}</div>
            </template>
            Bulls: {{b.bulls}}
            Cows: {{b.cows}}
          </div>
        </template>
      </div>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'bullsAndCows',
  components: { Collapse2 },
  data: function () {
    return {
      n: 4,
      p: 0,
      maxTurn: 12,
      turn: 0,
      board: [
      ],
      goal: [],
      vals: [
        { v: '0', },
        { v: '1', },
        { v: '2', },
        { v: '3', },
        { v: '4', },
        { v: '5', },
        { v: '6', },
        { v: '7', },
        { v: '8', },
        { v: '9', }
      ],
      end: false,
      beginTime: 0,
      timer: '',
      goalTime: ''
    }
  },
  mounted: function () {
    setInterval(()=>{this.tick()},1000)
  },
  methods: {
    newGame: function () {
      this.beginTime = new Date().getTime()
      this.seed = []
      for (let i = 0; i < this.vals.length; i++) {
        this.seed.push(i)
      }
      for (let i = 0; i < this.seed.length; i++) {
        const r = Math.floor(Math.random() * this.seed.length)
        const tmp = this.seed[i]
        this.seed[i] = this.seed[r]
        this.seed[r] = tmp
      }
      this.goal.splice(0, this.goal.length)
      for (let i = 0; i < this.n; i++) {
        this.goal.push(this.seed[i])
      }
      this.board.splice(0, this.board.length)
      for (let t = 0; t < this.maxTurn; t++) {
        const b = {
          turn: t + 1,
          state: [],
          bulls: 0,
          cows: 0,
        }
        for (let i = 0; i < this.n; i++) {
          b.state.push('')
        }
        this.$set(this.board, t, b)
      }
      this.turn = 0
      this.p = 0
      this.end = false
    },
    append: function (v) {
      if (this.turn >= this.maxTurn) {
        return
      }
      const b = this.board[this.turn]
      if (this.p < this.n) {
        b.state[this.p] = v
        this.p++
      }
    },
    cls: function () {
      if (this.turn >= this.maxTurn) {
        return
      }
      this.p = 0
      const b = this.board[this.turn]
      for (let i = 0; i < this.n; i++) {
        b.state[i] = ''
      }
    },
    check: function () {
      if (this.turn >= this.maxTurn) {
        return
      }
      if (this.p != this.n) {
        return
      }
      const b = this.board[this.turn]
      let bulls = 0
      let cows = 0
      for (let i = 0; i < this.n; i++) {
        if (b.state[i] === this.goal[i]) {
          bulls++
        } else {
          for (let j = 0; j < this.n; j++) {
            if (i !== j && b.state[i] === this.goal[j]) {
              cows++
            }
          }
        }
      }
      b.bulls = bulls
      b.cows = cows
      if (bulls === this.n) {
        this.end = true
        this.goalTime = this.timer
      } else {
        this.turn++
        this.p = 0
      }
    },
    clear: function () {
      this.end = false
      this.newGame()
    },
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

  }
}
</script>

<style scoped>
div.turn {
  background: #ccc;
  border: 1px solid #999;
  text-align: right;
  margin: 1px;
  padding: 0 2px;
}
div.current {
  background: #fff;
}
div.cleard {
  background: #eee;
}
div.c {
  display: inline-block;
  margin: 2px;
  padding: 0.5em;
  border-radius: 0.5em;
  cursor: pointer;
  border: 1px solid #999;
  width: 1em;
  text-align: center;
  font-weight: 700;
}
div.cmd {
  display: inline-block;
  margin: 2px;
  padding: 0.5em;
  border-radius: 0.5em;
  cursor: pointer;
  border: 1px solid #999;
  text-align: center;
  font-weight: 700;
}
div.c0 {
  color: #924;
  background: #eab;
}
div.c1 {
  color: #942;
  background: #eba;
}
div.c2 {
  color: #972;
  background: #eda;
}
div.c3 {
  color: #792;
  background: #dea;
}
div.c4 {
  color: #392;
  background: #bea;
}
div.c5 {
  color: #295;
  background: #aec;
}
div.c6 {
  color: #298;
  background: #aee;
}
div.c7 {
  color: #269;
  background: #ace;
}
div.c8 {
  color: #229;
  background: #aae;
}
div.c9 {
  color: #629;
  background: #cae;
}
div.cc {
  color: #333;
  background: #eee;
}
div.cv {
  color: #ccc;
  background: #333;
}
div.end {
  -ms-filter: blur(3px);
  filter: blur(3px);
}
div.goal {
  position: relative;
  height: 0;
  z-index: 999;
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
