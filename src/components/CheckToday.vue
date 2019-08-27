<template>
  <div class="checkToday">
    <collapse2 title="毎日チェック">
      <div>
        <template v-if='checked'>
          <div class="checked">チェック済</div>
        </template>
        <template v-else>
          <el-button type="primary" @click='onCheck'>チェック</el-button>
        </template>
        <el-input type="textarea" :rows='5' :value='dates'/>
      </div>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'checkToday',
  components: { Collapse2 },
  data: function () {
    return {
      checkTimes: []
    }
  },
  computed: {
    checked: function () {
      if (this.checkTimes.length === 0) {
        return false
      }
      const d = new Date()
      const ymd = d.getFullYear() + '/' + this.n2(d.getMonth() + 1) + '/' + this.n2(d.getDate())
      const lymd = this.checkTimes[0].substring(0, 10)
      return lymd >= ymd
    },
    dates: function () {
      if (this.checkTimes.length === 0) {
        return ''
      }
      const vs = []
      for (let i = 0; i < 10; i++) {
        if (i < this.checkTimes.length) {
          vs.push(this.checkTimes[i])
        }
      }
      return vs.join('\n')
    }
  },
  mounted: function () {
    this.loadChecks()
  },
  methods: {
    onCheck: function () {
      const d = new Date()
      const ymdhm = d.getFullYear() + '/' + this.n2(d.getMonth() + 1) + '/' + this.n2(d.getDate()) + ' ' + this.n2(d.getHours()) + ':' + this.n2(d.getMinutes())
      this.checkTimes.splice(0, 0, ymdhm)
      this.storeChecks()
    },
    n2: function (n) {
      return (n < 10 ? '0' : '') + n
    },
    storeChecks: function () {
      if (window.localStorage) {
        window.localStorage.setItem('checks', JSON.stringify(this.checkTimes))
      }
    },
    loadChecks: function () {
      if (window.localStorage) {
        const json = window.localStorage.getItem('checks')
        if (json) {
          this.checkTimes = JSON.parse(json)
        }
      }
    }
  }
}
</script>

<style scoped>
div.checked {
  display: inline-block;
  border: 1px solid green;
  border-radius: 0.2em;
  background: #efe;
  padding: 0.2em;
}
</style>
