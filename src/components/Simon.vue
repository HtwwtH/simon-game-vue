<template>
  <div class="simon">
    <h1>{{ msg }}</h1>

    <audio ref="sound1" src="../assets/sounds/1.mp3"></audio>
    <audio ref="sound2" src="../assets/sounds/2.mp3"></audio>
    <audio ref="sound3" src="../assets/sounds/3.mp3"></audio>
    <audio ref="sound4" src="../assets/sounds/4.mp3"></audio>
    <div id="simon">
      <div id="counter">
        <span
          v-text="
            showError
              ? 'НЕВЕРНО... Можете попробовать еще'
              : 'Раунд: ' + displayCount
          "
        ></span>
      </div>

      <button id="start" class="start-btn" @click="start">Start</button>

      <div class="simon__board">
        <div
          class="button"
          :class="{ highlight: hlGreen }"
          id="green"
          @click="input(1)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlRed }"
          id="red"
          @click="input(2)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlYellow }"
          id="yellow"
          @click="input(3)"
        ></div>
        <div
          class="button"
          :class="{ highlight: hlBlue }"
          id="blue"
          @click="input(4)"
        ></div>
      </div>

      <div class="simon__lvl">
        <p>Уровень сложности</p>
        <input type="radio" id="easy" value="easy" v-model="lvl" />
        <label for="easy">Легкий</label>
        <br />
        <input type="radio" id="normal" value="normal" v-model="lvl" />
        <label for="normal">Средний</label>
        <br />
        <input type="radio" id="hard" value="hard" v-model="lvl" />
        <label for="hard">Сложный</label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Simon",
  props: {
    msg: String,
  },
  data() {
    return {
      lvl: "easy",
      started: false, // start when click start button
      count: 0, // count rounds
      series: [], // array of correct answers
      playingSeries: false,
      userInput: [],
      hlRed: false,
      hlGreen: false,
      hlYellow: false,
      hlBlue: false,
      allowInput: false,
      showError: false,
      showWin: false,
    };
  },
  computed: {
    displayCount() {
      if (this.count == 0) return "- -";
      else return this.count;
    },
  },
  methods: {
    reset() {
      // Reset the game state
      this.started = false;
      this.count = 0;
      this.series = [];
      this.userInput = [];
      this.allowInput = false;
      this.playingSeries = false;
      this.showError = false;
      this.showWin = false;
      this.hlGreen = false;
      this.hlRed = false;
      this.hlYellow = false;
      this.hlBlue = false;
    },
    input(tone) {
      if (!this.allowInput) return;
      this.playTone(tone);
      this.userInput.push(tone);
      // check user input
      if (
        this.userInput[this.userInput.length - 1] !=
        this.series[this.userInput.length - 1]
      ) {
        this.userInput = [];
        this.allowInput = false;
        this.showError = true;
        // if wrong, end the game
        setTimeout(this.reset, 2000);
        return;
      }
      // last user input in series
      if (this.userInput.length == this.series.length) {
        let self = this;
        this.userInput = [];
        setTimeout(function () {
          self.addTone();
          self.playSeries();
        }, 1000);
      }
    },
    start() {
      if (this.count == 0) {
        this.started = true;
        this.addTone();
        this.playSeries();
      }
    },
    addTone() {
      this.count++;
      this.series.push(this.randomTone());
    },
    playSeries() {
      this.allowInput = false;
      this.playingSeries = true;
      let self = this;
      let seriedelay = 500;
      let tonedelay = 1500;
      switch (this.lvl) {
        case "easy":
          tonedelay = 1500;
          break;
        case "normal":
          tonedelay = 1000;
          break;
        case "hard":
          tonedelay = 400;
          break;
      }

      this.series.forEach(function (tone, index, array) {
        if (index == array.length - 1)
          setTimeout(function () {
            if (self.started) {
              self.playTone(tone);
              self.allowInput = true;
              self.playingSeries = false;
            }
          }, seriedelay);
        else
          setTimeout(function () {
            self.playTone(tone);
          }, seriedelay);
        // delay depends on lvl difficulty
        seriedelay += tonedelay;
      });
      this.playingSeries = false;
    },
    clearHighlights() {
      this.hlGreen = false;
      this.hlRed = false;
      this.hlYellow = false;
      this.hlBlue = false;
    },
    // Plays the tone & highlights the color
    playTone(tone) {
      switch (tone) {
        case 1:
          this.$refs.sound1.pause();
          this.$refs.sound1.currentTime = 0;
          this.$refs.sound1.play();
          this.hlGreen = true;
          break;
        case 2:
          this.$refs.sound2.pause();
          this.$refs.sound2.currentTime = 0;
          this.$refs.sound2.play();
          this.hlRed = true;
          break;
        case 3:
          this.$refs.sound3.pause();
          this.$refs.sound3.currentTime = 0;
          this.$refs.sound3.play();
          this.hlYellow = true;
          break;
        case 4:
          this.$refs.sound4.pause();
          this.$refs.sound4.currentTime = 0;
          this.$refs.sound4.play();
          this.hlBlue = true;
          break;
      }
      setTimeout(this.clearHighlights, 750);
    },
    randomTone() {
      return Math.floor(Math.random() * 4) + 1;
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.simon__board {
  display: flex;
  flex-wrap: wrap;
  width: 305px;
  height: 310px;
  justify-content: space-between;
  align-items: center;
  margin: 0 auto;
}
.button {
  cursor: pointer;
  width: 150px;
  height: 150px;
  border-radius: 10px;
}
#green {
  background-color: #2c771d;
}
#red {
  background-color: #c91943;
}
#yellow {
  background-color: #eedc54;
}
#blue {
  background-color: #4756de;
}
#green.highlight {
  background-color: #81da6f;
}
#red.highlight {
  background-color: #ff6c8e;
}
#yellow.highlight {
  background-color: #fffbbd;
}
#blue.highlight {
  background-color: #a7b0ff;
}
.simon__board {
  margin-bottom: 24px;
}
input[type="radio"] {
  margin-right: 8px;
}
.start-btn {
  cursor: pointer;
  background-color: transparent;
  margin-top: 12px;
  margin-bottom: 24px;
  outline: none;
  border: 2px solid #2c3e50;
  border-radius: 3px;
  width: 100px;
  padding: 4px;
  transition: background-color 0.3s ease;
}
.start-btn:hover {
  background-color: #2c3e501b;
  transition: background-color 0.3s ease;
}
</style>
