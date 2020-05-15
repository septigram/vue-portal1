<template>
  <div id="app">
    <div style="float:right;">
      <el-button @click='showComponentEdit=true' size="mini"><fa icon="cog"/></el-button>
    </div>
    <template v-for='(component, ci) in components'>
      <li :key='ci'>
        <template v-if='component.checked'>
          <template v-if='component.type === "clock"'><clock/></template>
          <template v-else-if='component.type === "calendar"'><calendar/></template>
          <template v-else-if='component.type === "calcurator"'><calcurator/></template>
          <template v-else-if='component.type === "address-list"'><address-list/></template>
          <template v-else-if='component.type === "json-tool"'><json-tool/></template>
          <template v-else-if='component.type === "color-wheel"'><color-wheel/></template>
          <template v-else-if='component.type === "ascii"'><ascii/></template>
          <template v-else-if='component.type === "code-convert"'><code-convert/></template>
          <template v-else-if='component.type === "time-convert"'><time-convert/></template>
          <template v-else-if='component.type === "grep-tool"'><grep-tool/></template>
          <template v-else-if='component.type === "memo"'><memo/></template>
          <template v-else-if='component.type === "sub-frame"'><sub-frame/></template>
          <template v-else-if='component.type === "sudoku"'><sudoku/></template>
          <template v-else-if='component.type === "check-today"'><check-today/></template>
          <template v-else-if='component.type === "gen-password"'><gen-password/></template>
          <template v-else-if='component.type === "bulls-and-cows"'><bulls-and-cows/></template>
          <template v-else-if='component.type === "test"'><test/></template>
        </template>
      </li>
    </template>
    <el-dialog :visible.sync='showComponentEdit' title="表示コンポーネント">
      <div class="componentList">
        <draggable v-model='components' el="div" ghost="ghost">
          <template v-for='(component, ci) in components'>
            <div :key='ci'>
              <el-checkbox v-model='component.checked'>{{component.name}}</el-checkbox>
            </div>
          </template>
        </draggable>
      </div>
      <el-row type="flex" justify="center">
        <el-button type="primary" @click='update()'>閉じる</el-button>
      </el-row>
    </el-dialog>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
import clock from './components/Clock'
import calendar from './components/Calendar'
import jsonTool from './components/JsonTool'
import calcurator from './components/Calcurator'
import colorWheel from './components/ColorWheel'
import addressList from './components/AddressList'
import ascii from './components/Ascii'
import codeConvert from './components/CodeConvert'
import timeConvert from './components/TimeConvert'
import grepTool from './components/GrepTool'
import memo from './components/Memo'
import subFrame from './components/SubFrame'
import sudoku from './components/Sudoku'
import checkToday from './components/CheckToday'
import genPassword from './components/GenPassword'
import bullsAndCows from './components/BullsAndCows'
import test from './components/Test'

export default {
  name: 'app',
  components: {
    clock, calendar, jsonTool, calcurator, colorWheel, addressList,
    draggable, ascii, codeConvert, timeConvert, grepTool, memo, subFrame, checkToday, sudoku, genPassword, bullsAndCows, test },
  data: function () {
    return {
      showComponentEdit: false,
      components: [
        { name: '時計', type: 'clock', checked: true },
        { name: 'カレンダー', type: 'calendar', checked: true },
        { name: 'ブックマーク', type: 'address-list', checked: true },
        { name: '計算機', type: 'calcurator', checked: true },
        { name: 'JSON変換', type: 'json-tool', checked: true },
        { name: 'ASCII表', type: 'ascii', checked: true },
        { name: 'URL変換', type: 'code-convert', checked: true },
        { name: 'UNIX時間変換', type: 'time-convert', checked: true },
        { name: 'カラーホイール', type: 'color-wheel', checked: true },
        { name: 'grep', type: 'grep-tool', checked: true },
        { name: 'ちょいメモ', type: 'memo', checked: true },
        { name: '毎日チェック', type: 'check-today', checked: true },
        { name: 'パスワード生成', type: 'gen-password', checked: true },
        // { name: '', type: 'sub-frame', checked: true },
        { name: 'ナンプレ', type: 'sudoku', checked: true },
        { name: 'Bulls and Cows', type: 'bulls-and-cows', checked: true },
        // { name: '', type: 'test', checked: true }
      ]
    }
  },
  created: function () {
    if (window.localStorage) {
      const comp = window.localStorage.getItem('vue-portal1-comp')
      if (comp) {
        this.components = JSON.parse(comp)
        let hasBullsAndCows = false
        this.components.forEach((c) => { if (c.type === 'bulls-and-cows') { hasBullsAndCows = true; }})
        if (!hasBullsAndCows) {
          this.components.push({
            name: 'Bulls and Cows',
            type: 'bulls-and-cows',
            checked: true
          })
        }
      }
    }
  },
  methods: {
    update: function () {
      this.showComponentEdit = false
      if (window.localStorage) {
        window.localStorage.setItem('vue-portal1-comp', JSON.stringify(this.components))
      }
    }
  }
}
</script>

<style>
ul {
  display: inline-block;
  margin: 0;
  padding: 0;
  margin-block-start: 0;
  margin-block-end: 0;
  padding-inline-start: 0;
}
li {
  display: inline-block;
  color: #000;
  padding: 0;
  margin: 0;
}
.ghost {
  opacity: 0.5;
  background: #999;
}
div.el-collapse-item__content {
  padding-bottom: 0;
}
div.el-collapse-item__header {
  height: 32px;
  line-height: 32px;
}
</style>
