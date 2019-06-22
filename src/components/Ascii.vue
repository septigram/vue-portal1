<template>
  <div class="ascii">
    <collapse2 title="Asciiコード表">
      <table>
        <tr>
          <th></th>
          <template v-for='(col, ci) in cols'>
            <th :key='ci'>{{col.label}}</th>
          </template>
        </tr>
        <template v-for='(row, ri) in codeTable'>
          <tr :key='ri'>
            <th>{{row.label}}</th>
            <template v-for='(cell, ci) in row.cells'>
              <td :key='ci'>{{cell.label}}</td>
            </template>
          </tr>
        </template>
      </table>
      0x20-0x7e<br/>
      <input v-model='asciiText'/><br/>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'ascii',
  components: { Collapse2 },
  data: function () {
    return {
      asciiText: '',
      codeTable: [],
      cols: []
    }
  },
  mounted: function () {
    const vs = []
    for (let i = 0x20; i < 0x7f; i++) {
      const hex = i.toString(16)
      vs.push(decodeURIComponent('%' + hex))
    }
    this.asciiText = vs.join('')
    for (let x = 0; x < 16; x++) {
      this.cols.push({
        col: x,
        label: 'x' + x.toString(16)
      })
    }
    for (let y = 2; y < 8; y++) {
      const row = {
        row: y,
        label: y.toString(16) + 'x',
        cells: []
      }
      this.codeTable.push(row)
      for (let x = 0; x < 16; x++) {
        const n = y * 16 + x
        const hex = n.toString(16)
        row.cells.push({
          col: x,
          code: n,
          hex: hex,
          label: decodeURIComponent('%' + hex)
        })
      }
    }
  }
}
</script>

<style scoped>
div.ascii table, div.ascii th, div.ascii td {
  border-collapse: collapse;
  border: 1px solid gray;
  text-align: center;
}
</style>
