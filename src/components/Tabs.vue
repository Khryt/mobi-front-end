<template>
  <div style="width: 500px; max-width: 90vw;">
    <q-tabs v-model="selectedTab" inverted align="justify">
      <q-tab name="all" color="secondary" :count="notesCount" slot="title" icon="receipt" label="uncomplited" @click="showUnComplited()"/>
      <q-tab name="comlited" color="purple" slot="title" icon="done all" label="Comlited" @click="showComplited()"/>
      <!--q-tab name="add" color="amber" slot="title" icon="note add" label="Add new" @click="showAddDialog()"/-->
    </q-tabs>
    <q-scroll-area style="max-width: 90vw; max-height: 79vh; height: 4000px;" class="shadow-1">
      <!--style="bottom: 5px; width: 400px; max-width: 90vw; max-height: 75vh;" class="shadow-1"-->
      <!--style="top: 152px; left: 5px; right: 5px; bottom: 10px;" class="fixed shadow-1"-->
      <q-list inset-separator>
        <transition name="custom-classes-transition" enter-class="list-enter" enter-active-class="list-enter-active">
          <q-item v-show="showEmptyMessage">
            <q-item-main>
              <q-item-tile label :color="colorEmpty">{{emptyMessage}}</q-item-tile>
              <q-item-tile sublabel icon="insert emoticon" :color="colorEmpty"></q-item-tile>
            </q-item-main>
          </q-item>
        </transition>
        <!--transition-group name="custom-classes-transition" enter-class="list-enter" enter-active-class="list-enter-active" leave-to-class="list-leave-to" leave-active-class="list-leave-active"-->
          <q-item v-for="note in allNotes" :key="note._id" v-if="note.complit===complitFilter" multiline>
            <q-item-main>
              <q-item-tile label>{{note.title}}</q-item-tile>
              <q-item-tile sublabel lines="2">{{note.text}}</q-item-tile>
            </q-item-main>
            <q-item-side right icon="more_vert">
              <tabsPopover :id="note._id" :doneLabel="popoverLabel" @done="noteDone" @edit="showEditDialog" @delete="noteDelete"/>
            </q-item-side>
          </q-item>
        <!--/transition-group--> 
      </q-list>
    </q-scroll-area>
    <q-inner-loading :visible="showLoader">
        <q-spinner-gears size="100px" color="secondary"></q-spinner-gears>
    </q-inner-loading>
  </div>
</template>

<script>
import tabsPopover from './Tabs-popover.vue'
import {
  Dialog,
  QTabs,
  QTab,
  QTabPane,
  QList,
  QItem,
  QItemSeparator,
  QItemSide,
  QItemMain,
  QItemTile,
  QPopover,
  QScrollArea,
  QInnerLoading,
  QSpinnerGears,
  LocalStorage,
  Toast
} from 'quasar'
export default {
  props: {
    showAddDiag: {
      type: Boolean,
      default: false
    }
  },
  components: {
    QTabs,
    QTab,
    QTabPane,
    QList,
    QItem,
    QItemSeparator,
    QItemSide,
    QItemMain,
    QItemTile,
    QPopover,
    QScrollArea,
    QInnerLoading,
    QSpinnerGears,
    tabsPopover
  },
  data () {
    return {
      selectedTab: 'all',
      emptyMessage: 'Empty',
      popoverLabel: 'Complite',
      colorEmpty: 'secondary',
      complitFilter: 'F',
      serverUrl: '',
      notesCount: 0,
      showEmptyMessage: false,
      readNext: false,
      showLoader: false,
      notes: [],
      allNotes: [],
      editNote: {},
      addNote: {}
    }
  },
  watch: {
    showAddDiag: function (val) {
      if (val) this.showAddDialog()
    }
  },
  methods: {
    showComplited: function () {
      this.complitFilter = 'T'
      this.showEmptyListMessage()
      this.popoverLabel = 'Uncomplite'
      this.colorEmpty = 'purple'
    },

    showUnComplited: function () {
      this.complitFilter = 'F'
      this.showEmptyListMessage()
      this.popoverLabel = 'Complite'
      this.colorEmpty = 'secondary'
    },

    getAllNotes: function () {
      this.showLoader = true
      this.$http
        .get(
          // GET /all notes
          this.serverUrl + '/notes/'
        )
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              this.allNotes = response.data
              this.notesCount = this.allNotes.filter(obj => obj.complit === 'F').length
              this.showEmptyListMessage()
              this.showLoader = false
            }
            else {
              alert('Server send this error:' + response.data.error)
            }
          },
          error => {
            alert('Error:' + error.data)
          }
        )
    },

    noteDone: function (id) {
      var cBool = (this.popoverLabel === 'Complite') ? 'T' : 'F'
      var index = this.getAllNotesIndex(id)
      this.allNotes[index].complit = cBool
      this.showActualNotesList()
      this.$http
        .put(this.serverUrl + '/note/' + id, { complit: cBool })
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              if (cBool === 'F') this.notesCount++
              else this.notesCount--
            }
            else {
              alert('Server send this error:' + response.data.error)
            }
          },
          error => {
            alert('Error:' + error.data)
          }
        )
    },

    noteAdd: function (data) {
      this.addNote = data
      this.$http
        .post(this.serverUrl + '/note/', {
          title: this.addNote.title,
          body: this.addNote.body
        })
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              this.allNotes.push(response.data)
              this.selectedTab = 'all'
              this.showActualNotesList()
              this.addNote = {}
              this.showEmptyMessage = false
              this.notesCount++
            }
            else {
              alert('Server send this error:' + response.data.error)
            }
          },
          error => {
            alert('Error:' + error.data)
          }
        )
    },

    noteEdit: function (data) {
      this.editNote = data
      var index = this.getAllNotesIndex(this.editNote.id)
      this.allNotes[index].title = this.editNote.title
      this.allNotes[index].text = this.editNote.body // !!
      this.showActualNotesList()
      this.$http
        .put(this.serverUrl + '/note/' + this.editNote.id, {
          title: this.editNote.title,
          body: this.editNote.body
        })
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              this.editNote = {}
            }
            else {
              alert('Server send this error:' + response.data.error)
            }
          },
          error => {
            alert('Error:' + error.data)
          }
        )
    },

    noteDelete: function (id) {
      var index = this.getAllNotesIndex(id)
      var count = (this.allNotes[index].complit === 'F') ? this.notesCount - 1 : this.notesCount
      this.allNotes.splice(index, 1)
      this.showActualNotesList()
      this.$http
        .delete(this.serverUrl + '/note/' + id)
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              this.notesCount = count
            }
            else {
              alert('Server send this error:' + response.data.error)
            }
          },
          error => {
            alert('Error:' + error.data)
          }
        )
    },

    showEditDialog: function (id) {
      var self = this
      var index = this.getAllNotesIndex(id)
      var title = this.allNotes[index].title
      var text = this.allNotes[index].text
      Dialog.create({
        title: 'Edit the note',
        //  message: 'Bla-bla',
        form: {
          title: {
            type: 'text',
            label: 'Title',
            model: title
          },
          body: {
            type: 'textarea',
            label: 'Text',
            model: text
          },
          id: { model: id }
        },
        buttons: [
          'Cancel',
          {
            label: 'Ok',
            preventClose: true,
            handler (data, close) {
              if (data.body.length > 0) {
                self.noteEdit(data)
                close()
                return
              }
              Toast.create('Text can not be empty...')
            }
          }
        ]
      })
    },

    showAddDialog: function () {
      var self = this
      Dialog.create({
        title: 'Add a new note',
        //  message: 'Bla-bla',
        form: {
          title: {
            type: 'text',
            label: 'Title',
            model: ''
          },
          body: {
            type: 'textarea',
            label: 'Text',
            model: ''
          }
        },
        buttons: [
          {
            label: 'Cancel',
            handler () {
              self.$emit('addCancel')
            }
          },
          {
            label: 'Ok',
            preventClose: true,
            handler (data, close) {
              if (data.body.length > 0) {
                self.noteAdd(data)
                self.$emit('addOk')
                close()
                return
              }
              Toast.create('Text can not be empty...')
            }
          }
        ]
      })
    },

    showEmptyListMessage: function () {
      if (this.allNotes.filter(obj => obj.complit === this.complitFilter).length <= 0) this.showEmptyMessage = true
      else this.showEmptyMessage = false
    },

    getAllNotesIndex: function (id) {
      return this.allNotes.map(function (e) { return e._id }).indexOf(id)
    },

    showActualNotesList: function () {
      if (this.selectedTab === 'comlited') this.showComplited()
      else this.showUnComplited()
    }
  },

  created: function () {
    this.serverUrl = LocalStorage.get.item('URL')
    this.getAllNotes()
    this.showUnComplited()
  }
}
</script>

<style lang='stylus'>
.docs-tab-pane {
  .q-tab-pane {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 65px;
  }
}

.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active, .list-leave-active {
  transition: all .3s;
}
.list-enter, .list-leave-to {
  opacity:.3;
  transform: translateX(50px);
}
</style>