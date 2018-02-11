<template>
    <div>
      <!--
        Use <q-side-link> component
        instead of <q-item> for
        internal vue-router navigation
      -->
      <q-list no-border link inset-delimiter>
        <q-list-header>Menu</q-list-header>
        <q-item>
          <q-item-side icon="receipt" />
          <q-item-main><q-side-link to="/NotesList">NotesList</q-side-link></q-item-main>
        </q-item>
        <q-item>
          <q-item-side icon="settings" />
          <q-item-main><q-side-link to="/Settings">Settings</q-side-link></q-item-main>
        </q-item>
        <q-item>
          <q-item-side icon="lock outline" />
          <q-item-main><q-side-link to="/">Logout</q-side-link></q-item-main>
        </q-item>
      </q-list>
    </div>
</template>

<script>
import {
  openURL,
  QList,
  QListHeader,
  QItem,
  QItemSide,
  QItemMain,
  QSideLink
} from 'quasar'
export default {
  components: {
    QList,
    QListHeader,
    QItem,
    QItemSide,
    QItemMain,
    QSideLink
  },
  methods: {
    launch (url) {
      openURL(url)
    }
  }
}
</script>