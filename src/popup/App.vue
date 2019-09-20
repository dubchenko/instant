<template>
  <div class="container">
    <div class="main">
      <input 
        v-model="useDarkTheme"
        type="checkbox" />
      <p>Use dark theme</p>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      useDarkTheme: false
    }
  },
  watch: {
    useDarkTheme (bool) {
      const theme = bool ? 'theme-dark' : 'theme-light'

      this.setTheme(theme)
    }
  },
  mounted () {
    this.init()
  },
  methods: {
    init () {
      chrome.storage.sync.get(['theme'], (result) => {
        if (!result.theme) {
          return
        }

        this.useDarkTheme = result.theme === 'theme-dark'
      })
    },
    setTheme (theme) {
      chrome.storage.sync.set({ theme: theme }, () => {})
    }
  },
}
</script>

<style lang="scss">
  *,
  *:before,
  *:after {
    -webkit-box-sizing: border-box;
    -moz-box-sizing: border-box;
    -ms-box-sizing: border-box;
    -o-box-sizing: border-box;
    box-sizing: border-box;
  }

  html,
  body {
    -webkit-font-smoothing: antialiased;
    margin: 0;
    font-size: 100%;
    font-family: 'Avenir';
  }

  .container {
    padding: .25rem 1.25rem;

    .main {
      white-space: nowrap;
      display: flex;
      align-items: center;
    }
  }
</style>
