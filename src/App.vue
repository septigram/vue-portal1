<template>
  <div id="app">
    <draggable v-model='components' el="ul" ghost="ghost">
      <template v-for='(component, ci) in components'>
        <template v-if='component.type === "clock"'>
          <li :key='ci'><clock/></li>
        </template>
        <template v-else-if='component.type === "calendar"'>
          <li :key='ci'><calendar/></li>
        </template>
        <template v-else-if='component.type === "calendar"'>
          <li :key='ci'><calcurator/></li>
        </template>
        <template v-else-if='component.type === "address-list"'>
          <li :key='ci'><address-list/></li>
        </template>
        <template v-else-if='component.type === "json-tool"'>
          <li :key='ci'><json-tool/></li>
        </template>
        <template v-else-if='component.type === "color-wheel"'>
          <li :key='ci'><color-wheel/></li>
        </template>
        <template v-else-if='component.type === "ascii"'>
          <li :key='ci'><ascii/></li>
        </template>
        <template v-else-if='component.type === "code-convert"'>
          <li :key='ci'><code-convert/></li>
        </template>
        <template v-else-if='component.type === "time-convert"'>
          <li :key='ci'><time-convert/></li>
        </template>
        <template v-else-if='component.type === "test"'>
          <li :key='ci'><test/></li>
        </template>
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
import test from './components/Test'

export default {
  name: 'app',
  components: { clock, calendar, jsonTool, calcurator, colorWheel, addressList, draggable, ascii, codeConvert, timeConvert, test },
  data: function () {
    return {
      components: [
        { type: 'clock' },
        { type: 'calendar' },
        { type: 'calcurator' },
        { type: 'address-list' },
        { type: 'json-tool' },
        { type: 'ascii' },
        { type: 'code-convert' },
        { type: 'time-convert' },
        { type: 'color-wheel' },
        { type: 'test' }
      ]
    }
  }
}
</script>

<style>
ul {
  display: inline-block;
  margin: 0;
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
</style>
