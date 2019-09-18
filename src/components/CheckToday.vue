<template>
  <div class="checkToday">
    <collapse2 title="毎日チェック" ref="c" :isOpen='store.openWhenNoCheck && !checked'>
      <div>
        <template v-if='checked'>
          <div class="checked">チェック済</div>
        </template>
        <template v-else>
          <el-button type="primary" @click='onCheck'>チェック</el-button>
        </template>
        &nbsp;
        <el-checkbox v-model='store.openWhenNoCheck' @change='storeChecks()'><span style="font-size:small;"> 未チェック時に開く</span></el-checkbox>
        <el-input type="textarea" :rows='5' v-model='store.checkTimes' @change='storeChecks()'/>
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
      store: {
        openWhenNoCheck: true,
        checkTimes: ''
      }
    }
  },
  computed: {
    checked: function () {
      if (this.store.checkTimes.length === 0) {
        return false
      }
      const d = new Date()
      const ymd = d.getFullYear() + '/' + this.n2(d.getMonth() + 1) + '/' + this.n2(d.getDate())
      const lines = this.store.checkTimes.split('\n')
      const lymd = lines[0].substring(0, 10)
      return lymd >= ymd
    }
  },
  created: function () {
    this.loadChecks()
  },
  methods: {
    onCheck: function () {
      const d = new Date()
      const ymdhm = d.getFullYear() + '/' + this.n2(d.getMonth() + 1) + '/' + this.n2(d.getDate()) + ' ' + this.n2(d.getHours()) + ':' + this.n2(d.getMinutes())
      this.store.checkTimes = ymdhm + '\n' + this.store.checkTimes
      this.storeChecks()
      this.$refs.c.setState(false)
    },
    n2: function (n) {
      return (n < 10 ? '0' : '') + n
    },
    storeChecks: function () {
      if (window.localStorage) {
        window.localStorage.setItem('checks', JSON.stringify(this.store))
      }
    },
    loadChecks: function () {
      if (window.localStorage) {
        const json = window.localStorage.getItem('checks')
        if (json) {
          this.store = JSON.parse(json)
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
  color: green;
  border-radius: 0.3em;
  background: #efe;
  padding: 0.5em 1em;
}
</style>
