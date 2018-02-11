<template>
  <q-layout ref="layout" view="lHh Lpr fff" :left-class="{'bg-grey-2': true}">
    <q-toolbar slot="header" inverted color="dark">
      <q-btn flat @click="$refs.layout.toggleLeft()">
        <q-icon name="menu" />
      </q-btn>
      <q-toolbar-title>
        Notes
      </q-toolbar-title>
      <q-btn color="secondary" icon-right="note add" @click="showAddDiag=true">new</q-btn>
    </q-toolbar>
    <leftSlot slot="left"/>
    <tabs :showAddDiag="showAddDiag" @addCancel="showAddDiag=false" @addOk="showAddDiag=false"/>
  </q-layout>
</template>

<script>
import tabs from './Tabs.vue'
import leftSlot from './MenuLeft.vue'
import {
  QLayout,
  QToolbar,
  QToolbarTitle,
  QBtn,
  QIcon
} from 'quasar'

export default {
  name: 'index',
  components: {
    QLayout,
    QToolbar,
    QToolbarTitle,
    QBtn,
    QIcon,
    tabs,
    leftSlot
  },
  data () {
    return {
      showAddDiag: false
    }
  },
  methods: {
    mounted () {
      this.$nextTick(() => {})
    },
    beforeDestroy () {}
  }
}
</script>

<style lang='stylus'>
</style>
