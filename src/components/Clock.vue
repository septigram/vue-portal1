<template>
  <div class="clock">
    <el-collapse v-model="visible">
      <el-collapse-item title="■時計" name="clock">
        <canvas ref="clock1" width="120" height="120"/><br/>
        <div class="disp">{{now|ftime}}</div>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script>
export default {
  name: 'clock',
  data: function () {
    return {
      visible: ['clock'],
      now: new Date()
    }
  },
  mounted: function () {
    this.tick(this)
    const self = this
    setInterval(function () { self.tick(self) }, 1000)    
  },
  methods: {
    tick: function (self) {
      self.now = new Date()
      self.draw(self)
    },
    draw: function (self) {
      const clock1 = self.$refs.clock1
      if (!clock1) {
        return
      }
      const g = clock1.getContext('2d')
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
      g.arc(cx, cy, r, 0, Math.PI * 2, false)
      g.stroke()
      g.beginPath()
      g.lineWidth = 3
      for (let i = 0; i < 60; i++) {
        const th = i * 2 * Math.PI / 60
        self.drawHand(g, th, cx, cy, i % 5 === 0 ? 48 : 51, 54)
      }
      g.stroke()
      const h = this.now.getHours()
      const m = this.now.getMinutes()
      const s = this.now.getSeconds()
      const vs = [
        { th: (h + m / 60 - 3) * 2 * Math.PI / 12, lineWidth: 6, color: '#000', r1: 30, r2: -5 },
        { th: (m - 15) * 2 * Math.PI / 60, lineWidth: 4, color: '#000', r1: 50, r2: -5 },
        { th: (s - 15) * 2 * Math.PI / 60, lineWidth: 2, color: '#c00', r1: 45, r2: -5, r3: 5 },
      ]
      vs.forEach((v) => {
        g.lineWidth = v.lineWidth
        g.strokeStyle = v.color
        g.beginPath()
        this.drawHand(g, v.th, cx, cy, v.r1, v.r2)
        g.stroke()
        if (v.r3) {
          g.beginPath()
          g.fillStyle = v.color
          g.arc(cx + Math.cos(v.th) * v.r1, cy + Math.sin(v.th) * v.r1, v.r3, 0, 2 * Math.PI, false)
          g.fill()
        }
      })
    },
    drawHand: function (g, th, cx, cy, r1, r2) {
      const thx = Math.cos(th)
      const thy = Math.sin(th)
      const x0 = cx + thx * r1
      const y0 = cy + thy * r1
      const x1 = cx + thx * r2
      const y1 = cy + thy * r2
      g.moveTo(x0, y0)
      g.lineTo(x1, y1)
    }
  },
  filters: {
    ftime: function (d) {
      const h = d.getHours()
      const m = d.getMinutes()
      const s = d.getSeconds()
      return (h < 10 ? '0' : '') + h + ':' + (m < 10 ? '0' : '') + m + ':' + (s < 10 ? '0' : '') + s;
    }
  }
}
</script>

<style scoped>
div.clock {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
div.disp {
  width: 120px;
  text-align: center;
  font-size: small;
  font-weight: bold;
}
</style>