<template>
  <q-layout ref="layout" view="lHh Lpr fff" :left-class="{'bg-grey-2': true}">
    <q-toolbar slot="header">
      <q-btn flat @click="$refs.layout.toggleLeft()">
        <q-icon name="menu" />
      </q-btn>
      <q-toolbar-title>
        Settings
      </q-toolbar-title>
    </q-toolbar>
    <leftSlot slot="left"/>
    <settings/>
  </q-layout>
</template>

<script>
import leftSlot from './MenuLeft.vue'
import settings from './Settings.vue'
import {
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
  QIcon
} from 'quasar'

export default {
  name: 'settings-win',
  components: {
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn,
    QIcon,
    leftSlot,
    settings
  },
  data () {
    return {
    }
  },
  watch: {
    URL: function (val, oldVal) {
      if (this.urlTest(val)) this.showErr('', false)
      else this.showErr('URL is invalid!', true)
    }
  },
  methods: {
    mounted () {
      this.$nextTick(() => {})
    },
    beforeDestroy () {},

    connectionTest (e, done) {
    }
  }
}
</script>

<style lang='stylus'>
</style>