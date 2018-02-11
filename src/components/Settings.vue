<template>
  <div class="layout-padding docs-input row justify-center">
      <div style="width: 500px; max-width: 90vw;">
          <q-field :error="error" helper="Notes REST API server URL" :error-label="errorText">
              <q-input v-model="URL" :inverted="testOk" :color="urlColor" float-label="URL" v-bind:pattern="expression" type="url" :after="[{icon: 'done', condition: testOk}]"/>
          </q-field>
          <q-btn loader color="primary" @click="connectionTest" v-if="showTestButton">
          Connection test
          <span slot="loading">
              <q-spinner-hourglass class="on-left" />
              Trying to connect...
          </span>
          </q-btn>
      </div>
  </div>
</template>

<script>
import {
  QBtn,
  QIcon,
  QItem,
  QField,
  QInput,
  QSpinnerHourglass,
  LocalStorage
} from 'quasar'

export default {
  name: 'settings',
  components: {
    QBtn,
    QIcon,
    QItem,
    QField,
    QInput,
    QSpinnerHourglass
  },
  data () {
    return {
      URL: '',
      errorText: '',
      expression: '^(http[s]?:\\/\\/(www\\.)?)([0-9A-Za-z-\\.@:%_+~#=]+)+((\\.[a-zA-Z]{2,3})+)(/(.)*)?(\\?(.)*)?',
      urlColor: 'primary',
      error: false,
      showTestButton: false,
      testOk: false
    }
  },
  watch: {
    URL: function (val) {
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
      console.log('URL' + this.URL)
      this.$http
        .get(
          this.URL + '/test/'
        )
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'FAIL') {
              console.log('OK')
              done()
              this.showTestButton = false
              this.testOk = true
              this.urlColor = 'secondary'
              LocalStorage.set('URL', this.URL)
              this.$emit('testIsOk')
            }
            else {
              //  console.log('Connection test is failed...')
              this.loginErr()
              done()
            }
          },
          error => {
            this.errorTxt = error.data
            done()
          }
        )
    },
    urlTest (str) {
      var regex = new RegExp(this.expression)
      if (str.match(regex)) return true
      else return false
    },
    showErr (txt, bool) {
      this.error = bool
      this.errorText = txt
      this.showTestButton = bool !== true
    }
  },

  created: function () {
    this.URL = LocalStorage.get.item('URL')
    if (typeof this.URL !== 'undefined' && this.URL !== '' && this.urlTest(this.URL)) this.showErr('', false)
    else this.showErr('URL is invalid!', true)
  }
}
</script>

<style lang='stylus'>
</style>