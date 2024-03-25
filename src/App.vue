<script>
import sound1 from "./assets/sounds_1.mp3"
import sound2 from "./assets/sounds_2.mp3"
import sound3 from "./assets/sounds_3.mp3"
import sound4 from "./assets/sounds_4.mp3"
export default {
  data() {
    return ({
      buttons : ["b",'r','y',"g"],
      currentButton : null,
      randomButtons : [],
      round : 1,
      isPlaying : false,
      sounds : [sound1, sound2, sound3, sound4],
      i : 0,
      gameWasStart : false,
      difficult : "normal",
      info : ""
    })
  },

  methods : {
    delay : async function (timeout) {
      return await new Promise((resolve) => {
        setTimeout(() => {
          resolve(0)
        }, timeout)
      })
    },

    playSound : async function (i) {
      return await new Promise(resolve => {
        this.isPlaying = true
        let audio = new Audio(this.sounds[i])
        audio.play()
        this.currentButton = this.buttons[i]

        audio.onended = () => {
          this.isPlaying = false
          resolve(this.buttons[i])
        }
      })
    },

    playRandom : async function () {
      let randomIndex = Math.floor(Math.random()* this.sounds.length)
      return await this.playSound(randomIndex)
    },

    start : async function () {
      this.i = 0
      this.gameWasStart = true
      this.isPlaying = true
      this.randomButtons = []
      this.info = `Раунд ${this.round}`
      await this.delay(500)
      for (let i = 0; i < this.round; ) {
        let random = await this.playRandom()
        this.randomButtons.push(random)
        this.currentButton = null
        await this.delay(this.timeout)
        i++
      }
      this.isPlaying = false
      
      return
    },

    pressGameControl : async function (i) {
      if (!this.isPlaying && this.gameWasStart) {
        let pressedBtn = await this.playSound(i)
        this.currentButton = null
        if (pressedBtn != this.randomButtons[this.i]) {
          this.info = `Вы проиграли на раунде ${this.round}. Попробуйте ещё раз`
          this.gameWasStart = false
          this.round = 1
          this.i = 0
        } else {
          this.i++

          if (this.i == this.round) {
            this.round++
            await this.start()
          }
        }
      }
    }
  },

  computed : {
    timeout () {
      switch (this.difficult) {
        case "easy" : return 1500
        case "normal" : return 1000
        case "hard" : return 400
      }
    }
  }
}
</script>

<template>
  <div class="app">
    <div class="game-control"
    >
      <div class="game-btn blue"
      :class="{active : currentButton == 'b'}"
      @click="pressGameControl(0)"
      ></div>
      <div class="game-btn red"
      :class="{active : currentButton == 'r'}"
      @click="pressGameControl(1)"
      ></div>
      <div class="game-btn yellow"
      :class="{active : currentButton == 'y'}"
      @click="pressGameControl(2)"
      ></div>
      <div class="game-btn green"
      :class="{active : currentButton == 'g'}"
      @click="pressGameControl(3)"
      ></div>
    </div>


    <div class="right">
      <h2 class="info"
          v-if="info"
          >
            {{ info }}
          </h2>

      <div v-if="!gameWasStart">
      <h2>Сложность</h2>
      <ul class="settings">
        
        <li><label for="easy">easy</label><input name="difficult" type="radio" id="easy" value="easy" v-model="difficult"></li>
        <li><label for="normal">normal</label><input name="difficult" type="radio" id="normal" value="normal" v-model="difficult"></li>
        <li><label for="hard">hard</label><input name="difficult" type="radio" id="hard" value="hard" v-model="difficult"></li>
      </ul>
      <button
        @click="start()"
        >Start</button>
      </div>
    </div>

    
    </div>
    

    
</template>

<style scoped>

.app {
  display: flex;
  justify-content: space-around;
}

.game-control {
  width: 200px;
  height: 200px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  border-radius: 100%;
  border: 1px solid silver;
  overflow: hidden;
}

.settings {
  display: flex;
  flex-direction: column;
  width: 200px;
}
.game-btn.active {
  filter: blur(5px);
}

.game-btn.blue {
  background: dodgerblue;
}

.game-btn.red {
  background: red;
}

.game-btn.yellow {
  background: yellow;
}

.game-btn.green {
  background: green;
}
</style>
