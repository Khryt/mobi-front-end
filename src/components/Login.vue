<template>
  <q-layout ref="layout" view="lHh Lpr fff" :left-class="{'bg-grey-2': true}">    
    <q-toolbar slot="header" inverted color="primary">
      <q-toolbar-title>
        Notes
      </q-toolbar-title>
    </q-toolbar>
    <div class="window-width row justify-center items-center" style="height: calc(100vh - 90px)">
      <q-card style="width: 90vw !important" >
        <q-card-title>
          Login
        </q-card-title>
        <q-card-main v-if="showLogin">
          <q-field helper="test">
            <q-input v-model="name" float-label="User name" :error="nameError" :class="{ 'animate-blink': error }"/>
          </q-field>
          <q-field helper="test">
            <q-input v-model="pwd" type="password" :error="pwdError" :class="{ 'animate-blink': error }" float-label="Password"/>
          </q-field>
          <q-btn loader color="primary"  @click="login">
            Login
            <span slot="loading">
              <q-spinner-hourglass class="on-left" />
              Loading...
            </span>
          </q-btn>
        </q-card-main>
        <q-card-main v-if="showSettings">
          <settings @testIsOk="loginOn(true)"/>
        </q-card-main>
      </q-card>
    </div>
  </q-layout>
</template>

<script>
import settings from './Settings.vue'
import {
  QLayout,
  QSideLink,
  QCard,
  QCardTitle,
  QCardSeparator,
  QCardMain,
  QField,
  QInput,
  QBtn,
  QToolbar,
  QToolbarTitle,
  QSpinnerHourglass,
  LocalStorage
} from 'quasar'
export default {
  name: 'login',
  components: {
    QLayout,
    QSideLink,
    QCard,
    QCardTitle,
    QCardSeparator,
    QCardMain,
    QField,
    QInput,
    QBtn,
    QToolbar,
    QToolbarTitle,
    QSpinnerHourglass,
    settings
  },
  data () {
    return {
      name: '',
      pwd: '',
      errorTxt: '',
      serverURL: '',
      error: false,
      nameError: false,
      pwdError: false,
      showLogin: true,
      showSettings: false
    }
  },

  methods: {
    login: function (e, done) {
      // console.log('login:' + e + ' ' + done)
      this.getServerUrl()
      if (this.name === '' || this.pwd === '') {
        if (this.name === '') this.nameErr()
        if (this.pwd === '') this.pwdErr()
        done()
        return
      }
      this.$http
        .get(
          // GET /all notes
          this.serverURL + '/user/' + this.name + '&' + this.pwd
        )
        .then(
          response => {
            if (typeof response.data !== 'undefined' && response.data !== 'Fail') {
              this.name = ''
              this.pwd = ''
              done()
              this.$router.push('/NotesList')
            }
            else {
              this.loginErr()
              done()
            }
          },
          error => {
            this.errorTxt = error.data
            if (!error.ok) this.loginOn(false)
            done()
          }
        )
    },

    loginErr () {
      this.error = true
      setTimeout(() => { this.error = false }, 2000)
      this.nameErr()
      this.pwdErr()
    },

    nameErr () {
      this.nameError = true
      setTimeout(() => { this.nameError = false }, 2500)
    },

    pwdErr () {
      this.pwdError = true
      setTimeout(() => { this.pwdError = false }, 2500)
    },

    loginOn (bool) {
      this.showLogin = bool
      this.showSettings = bool !== true
    },

    getServerUrl () {
      this.serverURL = LocalStorage.get.item('URL')
      if (typeof this.serverURL !== 'undefined' && this.serverURL !== '') this.loginOn(true)
      else this.loginOn(false)
    }
  }
}
</script>