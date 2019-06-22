<template>
  <div class="memo">
    <collapse2 title="ちょいメモ">
      <el-input type="textarea" :row='10' v-model="memo" @change="storeMemo()"/>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'memo',
  components: { Collapse2 },
  data: function () {
    return {
      memo: ''
    }
  },
  watch: {
    memo: function () {
      this.storeMemo()
    }
  },
  mounted: function () {
    this.loadMemo()
  },
  methods: {
    storeMemo: function () {
      if (window.localStorage) {
        window.localStorage.setItem('memo', this.memo)
      }
    },
    loadMemo: function () {
      if (window.localStorage) {
        this.memo = window.localStorage.getItem('memo')
      }
    }
  }
}
</script>

<style scoped>
</style>
