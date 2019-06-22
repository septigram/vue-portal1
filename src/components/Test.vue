<template>
  <div class="test">
    <collapse2 title="テスト">
      <el-switch v-model="cnv"/>
      <table>
        <tr>
          <template v-for='(cm, ci) in colorMap'>
            <td :key='ci'>
              <input v-model='cm.l' style="width:2em;"/>
            </td>
          </template>
        </tr>
        <template v-for='(lu,li) in lus'>
          <tr :key='li'>
            <template v-for='(cm, ci) in colorMap'>
              <td :style='{ background: c(cm, lus[li]) }' :key='ci'>{{li}}&nbsp;</td>
            </template>
          </tr>
        </template>
      </table>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'test',
  components: { Collapse2 },
  data: function () {
    return {
      cnv: false,
      lus: [],
      colorMap: [
        { h: 0, l:192 },
        { h: 30, l:192 },
        { h: 60, l:200 },
        { h: 90, l:192 },
        { h: 120, l:192 },
        { h: 150, l:192 },
        { h: 180, l:192 },
        { h: 210, l:192 },
        { h: 240, l:120 },
        { h: 270, l:140 },
        { h: 300, l:230 },
        { h: 330, l:192 }
      ]
    }
  },
  mounted: function () {
    for (let i = 0; i < 16; i++) {
      this.lus.push(i / 16)
    }
  },
  methods: {
    c: function (cm, lu) {
      const n = parseFloat(cm.l) / 255
      let l = Math.pow(lu, n)
      /*
      let l
      if (lu < 0.5) {
        l = 0.5 * n / (1 - lu)
      } else {
        l = 0.5 * (1 - n) / (lu - 0.5)
      }
      */
      // l = lu
      if (!this.cnv) {
        l = lu
      }
      return 'hsl(' + cm.h + ',100%,' + Math.floor(l*100) + '%)'
    }
  }
}
</script>

<style scoped>
tr {
  line-height: 1em;
}
td {
  line-height: 100%;
  padding: 0;
}
</style>
        