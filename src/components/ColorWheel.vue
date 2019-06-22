<template>
  <div class="colorWheel">
    <collapse2 title="カラーホイール">
      <div class="vBarBox">
        <canvas ref="canvas1" width="120" height="120" @click='setHue'/>
      </div>
      <div class="vBarBox">
        彩度<br/>
        <el-slider v-model="satuation" :max='100' @change='redraw()' vertical height="100px" style="margin: 10px 0 0 0;display: inline-block;"/>
      </div>
      <div class="vBarBox">
        明度<br/>
        <el-slider v-model="luminance" :max='100' @change='redraw()' vertical height="100px" style="margin: 10px 0 0 0;display: inline-block;"/>
      </div>
      <div style="display: inline-block;">
        <div class="disp" :style='{ background: value }'></div>
        <div class="disp" :style='{ background: hsl }'></div><br/>
        <el-input v-model='value' @change='setValue' style="width: 6em;"/><br/>
        <span style="font-family: monospace;font-size:small;">{{hsl}}</span><br/>
        <el-button ref="toolButton" size="mini" @click='flipTool' :type='showTool ? "success" : "normal"'>分割</el-button>
      </div>
      <template v-if="showTool">
        <el-form label-width="4em">
          <el-form-item label="分割数">
            <el-slider v-model="splits" :min='1' :max='24' @change='redraw()'/>
          </el-form-item>
          <el-form-item label="分散">
            <el-slider v-model="degree" :min='0' :max='360' @change='redraw()'/>
          </el-form-item>
          <template v-for='(col, ci) in cols'>
            <div class="disp2Box" :key='ci'>
              {{ci + 1}}:
              <div class="disp2" :style='{ background: col.rgb }'></div>
              {{ col.rgb }}
              <div class="disp2" :style='{ background: col.rgb2 }'></div>
              {{ col.rgb2 }}
            </div>
          </template>
        </el-form>
      </template>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'colorWheel',
  components: { Collapse2 },
  data: function () {
    return {
      showTool: false,
      value: '#000000',
      hue: 0,
      satuation: 100,
      luminance: 50,
      hsl: '#000000',
      splits: 5,
      degree: 160,
      cols: [],
      colorMap: null,
      colorMaps: [
        {
          name: 'Itten',
          reverse: true,
          mapping: [
            354, 329, 278, 226, 206, 199, 143, 80, 56, 34, 15, 2
          ]
        },
        {
          name: 'Itten2',
          reverse: false,
          mapping: [
            //354, 329, 278, 226, 206, 199, 143, 80, 56, 34, 15, 2
            354, 2, 15, 34, 56, 80, 143, 199, 206, 226, 278, 329
          ]
        },
        {
          name: 'PCCS',
          reverse: false,
          mapping: [
            351, 17, 37, 50, 66, 154, 175, 192, 208, 243, 294, 332
          ]
        }
      ]
    }
  },
  mounted: function () {
    this.colorMap = null // this.colorMaps[1]
    const self = this
    this.draw(self)

    // for (let i = 0; i < 36; i++) {
      // 
      // console.log(i + ':' + this.angleToHue(this.colorMap.mapping, i * 10))
    // }
  },
  methods: {
    flipTool: function () {
      this.showTool = !this.showTool
        this.redraw()
    },
    setValue: function () {
      let hsl = null
      if (this.value.match(/#[0-9a-fA-F]{6}/)) {
        const r = parseInt(this.value.substring(1, 3), 16)
        const g = parseInt(this.value.substring(3, 5), 16)
        const b = parseInt(this.value.substring(5, 7), 16)
        hsl = this.rgbToHsl(r, g, b)
      } else if (this.value.match(/#[0-9a-fA-F]{3}/)) {
        let r = parseInt(this.value.substring(1, 2), 16)
        let g = parseInt(this.value.substring(2, 3), 16)
        let b = parseInt(this.value.substring(3, 4), 16)
        r *= 0x11
        g *= 0x11
        b *= 0x11
        hsl = this.rgbToHsl(r, g, b)
      }
      if (hsl !== null) {
        this.hue = hsl.hue
        this.satuation = hsl.satuation
        this.luminance = hsl.luminance
        this.redraw()
      }
    },
    setHue: function (e) {
      const cx = 60
      const cy = 60
      const th = Math.atan2(e.offsetY - cy, e.offsetX - cx)
      this.hue = this.angleToHue(this.colorMap, (th / 2 / Math.PI * 360 + 360) % 360)
      this.redraw()
    },
    redraw: function () {
      this.draw(this)
    },
    draw: function (self) {
      const canvas1 = self.$refs.canvas1
      if (!canvas1) {
        return
      }
      const g = canvas1.getContext('2d')
      g.fillStyle = '#fff'
      g.rect(0, 0, 120, 120)
      g.fill()
      const cx = 60
      const cy = 60
      const r = cx - 4
      g.strokeStyle = '#000'
      g.lineWidth = 2
      g.lineCap = 'butt'
      g.beginPath()
      g.arc(cx, cy, r, 0, 2 * Math.PI, false)
      g.arc(cx, cy, r - 30, 0, 2 * Math.PI, false)
      g.stroke()
      const steps = 360
      for (let i = 0; i < steps; i++) {
        const ihue = Math.floor(360 * i / steps)
        const th = i / steps * 2 * Math.PI
        const thd = 2 * Math.PI / steps * 2
        g.fillStyle = 'hsl(' + self.angleToHue(self.colorMap, ihue) + ',' + Math.floor(self.satuation) + '%,' + Math.floor(self.luminance) + '%)'
        self.fillArc(g, th, thd, cx, cy, r, r - 30)
      }
      if (self.showTool) {
        g.strokeStyle = self.luminance > 50 ? '#666' : '#ccc'
        g.lineWidth = 2
        self.cols.splice(0, self.cols.length)
        for (let s = 0; s < self.splits; s++) {
          g.beginPath()
          const si = s - (self.splits - 1) / 2
          const hh = Math.floor(self.hue + self.degree / self.splits * si) % 360
          const thh = hh / 360 * 2 * Math.PI
          const hx = cx + Math.cos(thh) * (r - 15)
          const hy = cy + Math.sin(thh) * (r - 15)
          g.arc(hx, hy, 6, 0, 2 * Math.PI, false)
          g.stroke()
          const rgb = self.hslToRgb(hh, self.satuation, self.luminance)
          const rgb2 = '#' + rgb.substring(1, 2) + rgb.substring(3, 4) + rgb.substring(5, 6)
          self.cols.push({ rgb: rgb, rgb2: rgb2 })
        }
      }
      g.beginPath()
      g.strokeStyle = self.luminance > 50 ? '#333' : '#eee'
      g.lineWidth = 2
      const thh = self.hue / 360 * 2 * Math.PI
      const hx = cx + Math.cos(thh) * (r - 15)
      const hy = cy + Math.sin(thh) * (r - 15)
      g.arc(hx, hy, 10, 0, 2 * Math.PI, false)
      g.stroke()
      self.hsl = 'hsl(' + Math.floor(self.hue) + ',' + Math.floor(self.satuation) + '%,' + Math.floor(self.luminance) + '%)'
      self.value = self.hslToRgb(self.hue, self.satuation, self.luminance)
    },
    fillArc: function (g, th, thd, cx, cy, r1, r2) {
      const thx0 = Math.cos(th)
      const thy0 = Math.sin(th)
      const thx1 = Math.cos(th + thd)
      const thy1 = Math.sin(th + thd)
      const x0 = cx + thx0 * r1
      const y0 = cy + thy0 * r1
      const x1 = cx + thx0 * r2
      const y1 = cy + thy0 * r2
      const x2 = cx + thx1 * r1
      const y2 = cy + thy1 * r1
      const x3 = cx + thx1 * r2
      const y3 = cy + thy1 * r2
      g.beginPath()
      g.moveTo(x0, y0)
      g.lineTo(x1, y1)
      g.lineTo(x3, y3)
      g.lineTo(x2, y2)
      g.closePath()
      g.fill()
    },
    rgbToHsl: function (red, green, blue) {
      const r = red / 255
      const g = green / 255
      const b = blue / 255
      let cmax = Math.max(r, g, b)
      let cmin = Math.min(r, g, b)
      const hsl = { h: 0, s: 0, l: 0, hue: 0, satuation: 0, luminance: 0 }
      hsl.l = (cmax + cmin) / 2
      hsl.luminance = Math.floor(hsl.l * 100)
      const cd = cmax - cmin
      if (cd === 0) {
        return hsl
      }
      if (hsl.l < 0.5) {
        hsl.s = cd / (cmax + cmin)
      } else {
        hsl.s = cd / (2 - (cmax + cmin))
      }
      hsl.satuation = Math.floor(hsl.s * 100)
      if (r === cmax) {
        hsl.h = (g - b) / cd
      } else if (g === cmax) {
        hsl.h = 2 + (b - r) / cd
      } else if (b == cmax) {
        hsl.h = 4 + (r - g) / cd
      }
      hsl.h = Math.floor(hsl.h * 60 + 360) % 360
      hsl.hue = hsl.h
      return hsl
    },
    hslToRgb: function (hue, satuation, luminance) {
      const h = (hue + 360) % 360
      const s = satuation / 100
      const l = luminance / 100
      let cmin, cmax
      if (l <= 0.5) {
        cmin = l * (1 - s)
        cmax = 2 * l - cmin
      } else {
        cmax = l * (1 - s) + s
        cmin = 2 * l - cmax
      }
      const r = Math.floor(this.h2v((h + 120) % 360, cmin, cmax) * 255).toString(16)
      const g = Math.floor(this.h2v(h, cmin, cmax) * 255).toString(16)
      const b = Math.floor(this.h2v((h + 240) % 360, cmin, cmax) * 255).toString(16)
      return '#' + ('0' + r).slice(-2) + ('0' + g).slice(-2) + ('0' + b).slice(-2)
    },
    h2v: function (h, cmin, cmax) {
      if (h < 60) {
        return cmin + (cmax - cmin) * h / 60
      }
      if (h < 180) {
        return cmax
      }
      if (h < 240) {
        return cmin + (cmax - cmin) * (240 - h) / 60
      }
      return cmin
    },
    hueToAngle: function (hue) {
      return hue
    },
    angleToHue: function (colorMap, angle) {
      if (!colorMap) {
        return angle
      }
      const mapping = colorMap.mapping
      const n1 = angle / (360 / mapping.length)
      const n2 = Math.floor(n1)
      const m1 = mapping[n2]
      const m2 = mapping[(n2 + 1) % mapping.length]
      const d = m1 < m2 ? m2 - m1 : m2 + 360 - m1
      const c = Math.floor(m1 + (n1 - n2) * d)
      return (c + 360) % 360
    }
  }
}
</script>

<style scoped>
div.disp {
  display: inline-block;
  position: relative;
  left: 12px;
  width: 32px;
  height: 48px;
}
div.disp2Box {
  font-family: monospace;
  margin: 0 0 0 2em;
}
div.disp2 {
  display: inline-block;
  width: 1em;
  height: 1em;
}
div.vBarBox {
  display: inline-block;
  vertical-align: top;
  text-align: center;
}
</style>
