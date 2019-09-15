<template>
  <div class="main theme-dark">
    <div
      v-if="major && minor"
      class="timer">
      <div class="age">
        <span>age</span>
      </div>
      <div class="time">
        <span class="major">{{ major }}</span>
        <span class="minor">.{{ minor }}</span>
      </div>
    </div>
    <div v-if="noDateOfBirth">
      <form @submit.prevent="setDateOfBirth">
        <input type="date" v-model="dob">
        <input
          type="submit"
          value="Send">
      </form>
    </div>
    <div class="footer">
      <span @click="clearStorage">instant</span>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      inited: false,
      major: null,
      minor: null,
      noDateOfBirth: false,
      dob: null
    }
  },
  mounted () {
    this.init()
  },
  methods: {
    setDateOfBirth () {
      if (!this.dob) {
        return
      }

      const dob = new Date(this.dob).getTime()

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

        this.dob = result.dob

        this.startLoop()
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
      const duration = now - this.dob

      const years = duration / 31557600000

      const majorMinor = years.toFixed(9).toString().split('.')

      this.major = majorMinor[0]
      this.minor = majorMinor[1]
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

  .main {
    height: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;

    &.theme-dark {
      background: #2F3640;
      color: #F1F2F2;
    }

    &.theme-light {
      background: #F1F2F2;
      color: #2F3640;
    }

    .timer {
      display: flex;
      flex: 10;
      justify-content: center;
      flex-direction: column;

      .age {
        margin-left: .25rem;
        opacity: .5;
      }

      .time {
        display: flex;

        .major {
          font-size: 6rem;
          font-weight: 600;
          line-height: 1;
        }

        .minor {
          margin-left: .25rem;
          opacity: .5;
          font-size: 3rem;
          line-height: 1.2;
        }
      }
    }

    .footer {
      flex: 1;
    }
  }

</style>
