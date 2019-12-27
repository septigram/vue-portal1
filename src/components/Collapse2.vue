<template>
  <div class="collapse2">
    <div class="collapse2Header">
      <transition name="fade">
        <div v-if='state' class="collapse2Navi">
          <slot name="header"/>
        </div>
      </transition>
      <div v-if='showHeader' @click='onclick()' class="collapse2Title">
        <div :class='{ hicon: true, arrowRot: state}'><fa icon="angle-right"/></div>
        &nbsp;{{title}}
      </div>
      <br style="clear:both;"/>
    </div>
    <div :class='{collapse2Slot: true, collapse2SlotShow: state}'>
      <slot/>
    </div>
  </div>
</template>

<script>
export default {
  name: 'collapse2',
  props: {
    title: {
      type: String
    },
    isOpen: {
      type: Boolean,
      default: false
    },
    showHeader: {
      type: Boolean,
      default: true
    }
  },
  data: function () {
    return {
      state: this.isOpen
    }
  },
  methods: {
    onclick: function () {
      this.state = !this.state
    },
    setState: function (b) {
      this.state = b
    }
  }
}
</script>

<style scoped>
div.collapse2 {
  border: 1px solid gray;
  border-radius: 0.5em;
  margin: 0.1em;
  padding: 0.1em;
  vertical-align: top;
  display: inline-block;
  box-shadow: 2px 2px 2px rgba(0,0,0,0.4)
}
div.collapse2Navi {
  float:right;
}
div.collapse2Header {
  user-select: none;
  height: 2em;
}
div.collapse2Title {
  padding: 0.3em;
  cursor: pointer;
  display: inline-block;
}
div.hicon {
  display: inline-block;
  font-weight: bold;
  transition: transform 0.3s ease 0s;
}
div.arrowRot {
  transform: rotate(90deg);
}
div.collapse2Slot {
  transition: max-width 1s ease 0s, max-height 1s ease 0s, ;
  max-height: 0;
  max-width: 0;
  overflow: hidden;
}
div.collapse2SlotShow {
  max-height: 800px;
  max-width: 1024px;
  overflow: auto;
}
div.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
div.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
