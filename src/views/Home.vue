<template>
  <div class="home">
    <address-list/>
  </div>
</template>

<script>
// @ is an alias to /src
import AddressList from '@/components/AddressList.vue'

export default {
  name: 'home',
  components: {
    AddressList
  },
  data: function () {
    return {
      memo: 'memo1',
    }
  }
}
</script>

