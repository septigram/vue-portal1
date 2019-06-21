<template>
  <div class="subFrame">
    <collapse2 :title='frameName'>
      <iframe width="300" height="300" :href='href' sandbox>
      </iframe>
    </collapse2>
  </div>
</template>

<script>
import Collapse2 from './Collapse2'

export default {
  name: 'subFrame',
  components: { Collapse2 },
  data: function () {
    return {
      frameName: 'フレーム',
      href: 'https://tokyo-ame.jwa.or.jp/'
    }
  }
}
</script>

<style scoped>
</style>
