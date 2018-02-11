<template>
  <q-popover ref="popover">
    <q-list link>
      <q-item @click="$emit('done', id), $refs.popover.close()">
        <q-item-main :label="doneLabel" />
      </q-item>
      <q-item @click="$emit('edit', id), $refs.popover.close()">
        <q-item-main label="Edit" />
      </q-item>
      <q-item @click="$emit('delete', id), $refs.popover.close()">
        <q-item-main label="Delete" />
      </q-item>
    </q-list>
  </q-popover>
</template>

<script>
import {
  QList,
  QItem,
  QItemMain,
  QPopover
} from 'quasar'
export default {
  props: {
    id: {
      type: String,
      default: ''
    },
    doneLabel: {
      type: String,
      default: ''
    }
  },
  components: {
    QList,
    QItem,
    QItemMain,
    QPopover
  }
}
</script>