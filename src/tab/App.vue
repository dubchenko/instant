<template>
  <div class="main theme-dark">
    <div class="timer">
      <div class="age">
        <span>age</span>
      </div>
      <div class="time">
        <span class="major">{{ major }}</span>
        <span class="minor">.{{ minor }}</span>
      </div>
    </div>
    <div class="footer">
      instant
    </div>
    <!-- <div v-else>
      :(
    </div>
    <button @clsick="clearStorage">Clear Storage</button> -->
  </div>
</template>

<script>
export default {
  data () {
    return {
      inited: false,
      major: null,
      minor: null,
      testdate: null,
      message: 'Hello World!',
      age: new Date('1996/03/14')
    }
  },
  mounted () {
    this.init()
    // this.setDateOfBirth()
    setInterval(() => {
      this.test()
    }, 100);
  },
  methods: {
    setDateOfBirth () {   
      const dob = this.age / 1000 | 0
      chrome.storage.sync.set({ dob: dob }, () => {
        console.log('Value is set', dob);
      })
    },
    init () {
      chrome.storage.sync.get(['dob'], (result) => {
        if (result.dob) {
          this.inited = true
          this.test()
          return
        }
      })
    },
    test () {
      const now = new Date()
      const duration = now - this.age
      // const years = duration / 31556900000
      // 31536,000,000
      const newYears = duration / 31557600000
      
      const majorMinor = newYears.toFixed(9).toString().split('.')

      this.major = majorMinor[0]
      this.minor = majorMinor[1]
    },
    clearStorage () {
      chrome.storage.sync.clear();
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
