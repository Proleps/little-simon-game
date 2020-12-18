<template>
  <div id="app">
    <h1>Simon Says</h1>
    <div class="container">
      <div class="simon">
        <ul>
          <li class="red" data-tile="0" @click="() => this.playerTarget.next(0)"></li>
          <li class="blue" data-tile="1" @click="() => this.playerTarget.next(1)"></li>
          <li class="yellow" data-tile="2" @click="() => this.playerTarget.next(2)"></li>
          <li class="green" data-tile="3" @click="() => this.playerTarget.next(3)"></li>
        </ul>
      </div>
      
      <div class="game-info">
        <h2>Round: {{this.gameOrder.length}}</h2>
        <button @click="onStartButtonClickHandler">Start</button>
        <p v-if="result">Sorry, you lost<br>after {{result}} rounds!</p>

        
        <div class="game-options">
          <h2>Game Options:</h2>
          <label>
            <input type="radio" name="mode" @click="() => onLevelHandler(1500)" >Eazy<br>
          </label>
          <label>
            <input type="radio" name="mode" @click="() => onLevelHandler(1000)" checked >Normal<br>
          </label>
          <label>
            <input type="radio" name="mode" @click="() => onLevelHandler(400)" >hard<br>
          </label>
          <!-- <input type="radio" name="mode" value="sound-only">Sound Only<br>
          <input type="radio" name="mode" value="light-only">Light Only<br>
          <input type="radio" name="mode" value="free-board">Free board -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: { },
  data() {
    return {
      coloredPlayButtons: [],
      gameOrder: [],
      level: 1500,
      playerTarget: this.onSimonClickHandler(),
      result: null,
      timersFromGameSequence: []
    }
  },
  methods: {
    onLevelHandler(value) {
      this.level = value
    },
    clearGame() {
      this.gameOrder = []
      this.timersFromGameSequence.forEach( (item) => clearTimeout(item))
      this.coloredPlayButtons.forEach( (_, i) => {
        this.coloredPlayButtons[i].classList.remove('activated')
      })
    },
    onStartButtonClickHandler() {
      this.result = null
      this.clearGame()
      setTimeout( this.startGame, 300)
    },

    startGame() {
      this.gameOrder.push(Math.floor(Math.random()*4))
      this.gameSequence()
      setTimeout( () => {
        this.playerTarget = this.onSimonClickHandler()
        this.playerTarget.next('__INIT__')
      }, (this.gameOrder.length - 1) * this.level + 200 )
    },

    gameSequence() {
      this.timersFromGameSequence = []
      this.gameOrder.forEach( (item, i) => {
        this.timersFromGameSequence.push( setTimeout( () => {
          this.activateTile(item)
        } , i * this.level + 200 ))
      })
    },

    *onSimonClickHandler(targetArea) {
      for (const item in this.gameOrder) {
        const value = yield targetArea
        this.activeteSound(value)
        if (value !== this.gameOrder[item]) {
          this.result = this.gameOrder.length
          this.clearGame()
        } else {
          if ((+item + 1) === this.gameOrder.length) {
            setTimeout( () => this.startGame(), 1000 )
          }
        }
      }
    },

    activateTile(value) {
      this.coloredPlayButtons[value].classList.add('activated')
      this.activeteSound(value)
      setTimeout(() => {
        this.coloredPlayButtons[value].classList.remove('activated');
      }, (this.level - 100));
    },

    activeteSound(value) {
      const sound = new Audio()
      sound.src = require(`./assets/${value}.mp3`)
      sound.play()
    }
  },
  mounted() {
    this.coloredPlayButtons.push(document.querySelector("[data-tile='0']"))
    this.coloredPlayButtons.push(document.querySelector("[data-tile='1']"))
    this.coloredPlayButtons.push(document.querySelector("[data-tile='2']"))
    this.coloredPlayButtons.push(document.querySelector("[data-tile='3']"))
  }
}
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
  .container {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
    max-width: 768px;
    margin: 0 auto;
    padding-top: 60px;
  }
  .h1 {
    margin: 1em 0 2em 0;
    text-align: center;
  }
  .simon {
    background: #fff;
    position: relative;
    float: left;
    margin: 0 1em 0 1em;
    width: 302px;
    height: 295px;
    border-radius: 150px 150px 150px 150px;
    box-shadow: 2px 1px 12px #aaa;
  }
  ul, li {
    padding: 0;
    margin: 0;
  }
  ul {
    list-style: none;
  }
  .red {
    background: #FF5643;
    clip: rect(0px, 300px, 150px, 150px);
    width: 296px;
  }
  .blue {
    background: dodgerblue;
    clip: rect(0px, 150px, 150px, 0px);
    width: 300px;
  }
  .yellow {
    background: #FEEF33;
    clip: rect(150px, 150px, 300px, 0px);
    width: 300px;
  }
  .green {
    background: #BEDE15;
    clip: rect(150px,300px, 300px, 150px);
    width: 296px;
  }
  .red, .blue, .yellow, .green {
    opacity: 0.6;
    height: 290px;
    border-radius: 150px 150px 150px 150px;
    position: absolute;
    text-indent: 10000px;
    cursor: pointer;
  }
  .red:hover, .blue:hover, .yellow:hover, .green:hover {
    border: 2px solid black
  }
  .red:active, .blue:active, .yellow:active, .green:active {
    opacity: 1;
  }
  .activated {
    opacity: 1;
  }
  .game-info {
    margin: 0 1em 0 1em;
  }
  .game-info button {
    width: 5em;
    box-sizing: border-box;
    font-size: 1.4em;
    -webkit-border-radius: 10px 10px 10px 10px;
    border-radius: 10px 10px 10px 10px;
    background: #6DABE8;
    border: none;
    padding: 0.3em 0.6em;
  }
  .game-options h2 {
    margin-top: 30px;
    margin-bottom: 0;
  }
  .game-options input[type="radio"] {
    margin-right: 10px;
  }
  label:hover {
    cursor: pointer;
  }
</style>
