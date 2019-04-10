<template>
  <div class="ascii">
    <el-collapse v-model="visible">
      <el-collapse-item title="■Asciiコード表" name="t">
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
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'ascii',
  data: function () {
    return {
      asciiText: '',
      visible: [],
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
div.ascii {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}

div.ascii table, div.ascii th, div.ascii td {
  border-collapse: collapse;
  border: 1px solid gray;
  text-align: center;
}
</style>
