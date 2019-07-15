<template>
  <div id="app">
    <draggable v-model='components' el="ul" ghost="ghost">
      <template v-for='(component, ci) in components'>
        <li :key='ci'>
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
          <template v-else-if='component.type === "test"'><test/></template>
        </li>
      </template>
    </draggable>
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
import test from './components/Test'

export default {
  name: 'app',
  components: {
    clock, calendar, jsonTool, calcurator, colorWheel, addressList,
    draggable, ascii, codeConvert, timeConvert, grepTool, memo, subFrame, sudoku, test },
  data: function () {
    return {
      components: [
        { type: 'clock' },
        { type: 'calendar' },
        { type: 'address-list' },
        { type: 'calcurator' },
        { type: 'json-tool' },
        { type: 'ascii' },
        { type: 'code-convert' },
        { type: 'time-convert' },
        { type: 'color-wheel' },
        { type: 'grep-tool' },
        { type: 'memo' },
        // { type: 'sub-frame' },
        { type: 'sudoku' }
        // { type: 'test' }
      ]
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
