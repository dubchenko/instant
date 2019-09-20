<template>
  <div 
    class="container"
    :class="[ theme ]">
    <div class="main">
      <template v-if="isReady">
        <div class="sub">
          <span>age</span>
        </div>
        <div class="loop">
          <span class="ages">{{ time.age }}</span>
          <span class="millisecond">.{{ time.millisecond }}</span>
        </div>
      </template>
      <template v-if="noDateOfBirth">
        <form @submit.prevent="setDateOfBirth">
          <input type="date" v-model="dateOfBirth">
          <input
            type="submit"
            value="Submit">
        </form>
      </template>
    </div>
    <div class="footer">
      <a href="https://github.com/dubchenko/instant">instant</a>
      <span @click="clearStorage"></span>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      theme: 'theme-light',
      time: {},
      noDateOfBirth: false,
      dateOfBirth: null
    }
  },
  mounted () {
    this.init()
    this.initTheme()
  },
  computed: {
    isReady () {
      return ('age' in this.time) && ('millisecond' in this.time)
    }
  },
  methods: {
    setDateOfBirth () {
      if (!this.dateOfBirth) {
        return
      }

      const dob = new Date(this.dateOfBirth).getTime()

      chrome.storage.sync.set({ dob: dob }, () => {
        this.noDateOfBirth = false
        this.init()
      })
    },
    init () {
      chrome.storage.sync.get(['dob'], (result) => {
        if (!result.dob) {
          this.noDateOfBirth = true
          return
        }

        this.dateOfBirth = result.dob

        this.startLoop()
      })
    },
    initTheme () {
      chrome.storage.sync.get(['theme'], (result) => {        
        if (!result.theme) {
          return
        }

        this.theme = result.theme
      })
    },
    startLoop () {
      this.loop()

      setInterval(() => {
        this.loop()
      }, 100)
    },
    loop () {
      const now = new Date().getTime()
      const difference = now - this.dateOfBirth

      const fullYears = difference / 31557600000

      const fullYearsAndMillisecond = fullYears.toFixed(9).toString().split('.')

      this.time = {
        age: fullYearsAndMillisecond[0],
        millisecond: fullYearsAndMillisecond[1]
      }
    },
    clearStorage () {
      chrome.storage.sync.clear()
    }
  }
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
    height: 100%;
    font-size: 100%;
    font-family: 'Avenir';
  }

  .container {
    height: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;

    &.theme-dark {
      background: #1A1A1A;
      color: #F5F5F5;

      a {
        color: #F5F5F5;
      }

      input[type="date"] {
        color: #F5F5F5;
      }

      input[type="submit"] {
        color: #1A1A1A;
        background: #F5F5F5;
      }
    }

    &.theme-light {
      background: #F5F5F5;
      color: #1A1A1A;

      a {
        color: #1A1A1A;
      }

      input[type="date"] {
        color: #1A1A1A;
      }

      input[type="submit"] {
        color: #F5F5F5;
        background: #1A1A1A;
      }
    }

    .main {
      display: flex;
      flex: 12;
      justify-content: center;
      flex-direction: column;

      .sub {
        margin-left: .25rem;
        opacity: .5;
      }

      .loop {
        display: flex;

        .ages {
          font-size: 6rem;
          font-weight: 600;
          line-height: 1;
        }

        .millisecond {
          margin-left: .25rem;
          opacity: .5;
          font-size: 3rem;
          line-height: 1.2;
        }
      }
    }

    form {
      display: flex;
      flex-direction: column;

      input[type="date"]::-webkit-inner-spin-button {
        display: none;
      }

      input[type="date"] {
        font-size: 2rem;
        border: 0;
        background: transparent;
      }

      input[type="submit"] {
        cursor: pointer;
        margin-top: 1rem;
        opacity: .9;
        border: 0;
        font-weight: 600;
        padding: .5rem;
        font-size: 1rem;
        border-radius: .25rem;

        &:hover {
          opacity: 1;
        }
      }
    }

    .footer {
      flex: 1;

      a {
        text-decoration: none;
        opacity: .5;

        &:hover {
          opacity: 1;
        }
      }
    }
  }

</style>
