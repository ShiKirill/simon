<template>
  <div class="main">
    <div class="lose-window" v-if="loss" @click="loss = false">You lose :C</div>
    <h3 class="game-title">Simon</h3>
    <audio preload="auto" src="../assets/sounds/simonSound1.mp3"></audio>
    <div class="game-block">
      <div class="game-field">
        <div class="game-field__item">
          <button
            class="square square_green"
            @click="clickField(1)"
            :class="{ active: isActive[1] }"
            :disabled="!enableClick"
          ></button>
          <button
            class="square square_red"
            @click="clickField(2)"
            :class="{ active: isActive[2] }"
            :disabled="!enableClick"
          ></button>
          <button
            class="square square_yellow"
            @click="clickField(3)"
            :class="{ active: isActive[3] }"
            :disabled="!enableClick"
          ></button>
          <button
            class="square square_blue"
            @click="clickField(4)"
            :class="{ active: isActive[4] }"
            :disabled="!enableClick"
          ></button>
        </div>
      </div>
      <div class="game-info">
        <h3 class="score">{{ showScore }}</h3>
        <button class="start-btn" @click="startGame">{{ btnText }}</button>
        <h3 class="options">Difficulty:</h3>
        <div class="difficulty-choice">
          <input
            type="radio"
            id="easy"
            name="diff"
            class="diff-input"
            value="1500"
            @input="setTimeout"
          />
          <label for="easy" class="diff-label">Easy</label>
          <br />
          <input
            type="radio"
            id="normal"
            name="diff"
            class="diff-input"
            value="1000"
            @input="setTimeout"
            checked
          />
          <label for="normal" class="diff-label">Normal</label>
          <br />
          <input
            type="radio"
            id="hard"
            name="diff"
            class="diff-input"
            value="400"
            @input="setTimeout"
          />
          <label for="hard" class="diff-label">Hard</label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Game',
  data() {
    return {
      btnText: 'Start',
      playing: false,
      enableClick: false,
      score: 0,
      timeout: 1000,
      sequence: [],
      userSequence: [],
      loss: false,
      sequenceId: '',
      isActive: {
        1: false,
        2: false,
        3: false,
        4: false,
      },
      sounds: [
        new Audio('https://s3.amazonaws.com/freecodecamp/simonSound1.mp3'),
        new Audio('https://s3.amazonaws.com/freecodecamp/simonSound2.mp3'),
        new Audio('https://s3.amazonaws.com/freecodecamp/simonSound3.mp3'),
        new Audio('https://s3.amazonaws.com/freecodecamp/simonSound4.mp3'),
      ],
    };
  },
  computed: {
    showScore() {
      return 'Score: ' + this.score;
    },
  },
  methods: {
    setTimeout(e) {
      this.timeout = e.target.value;
      if (this.playing) {
        this.startGame();
      }
    },
    startGame() {
      this.playing = true;
      this.enableClick = true;
      this.btnText = 'Restart';
      this.score = 0;
      this.sequence = [];
      this.userSequence = [];
      clearInterval(this.sequenceId);
      this.startSequence();
    },
    clickField(index) {
      this.activateButton(index);
      this.playSound(index - 1);
      this.userSequence.push(index);
      this.checkResult();
    },
    playSound(index) {
      this.sounds[index].pause();
      this.sounds[index].currentTime = 0.0;
      this.sounds[index].play();
    },
    startSequence() {
      let currentIndex = 0;
      this.enableClick = false;
      this.sequence.push(Math.floor(Math.random() * 4 + 1));

      this.sequenceId = setInterval(() => {
        if (currentIndex >= this.sequence.length) {
          clearInterval(this.sequenceId);
          this.enableClick = true;
          return;
        }
        this.playSound(this.sequence[currentIndex] - 1);
        this.activateButton(this.sequence[currentIndex]);
        currentIndex++;
      }, this.timeout);
    },
    activateButton(index) {
      this.isActive[index] = true;
      setTimeout(() => {
        this.isActive[index] = false;
      }, 300);
    },
    checkResult() {
      for (let i = 0; i < this.userSequence.length; i++) {
        if (this.userSequence[i] !== this.sequence[i]) {
          this.sequenceId = '';
          this.enableClick = false;
          this.score = 0;
          this.playing = false;
          this.btnText = 'Start';
          this.loss = true;
          return;
        }
      }
      if (this.userSequence.length === this.sequence.length) {
        this.userSequence = [];
        this.score++;
        setTimeout(() => {
          this.startSequence();
        }, 200);
      }
    },
  },
};
</script>

<style scoped lang="scss">
$blue: #2525aa;
$green: #3ebb15;
$red: #c71a1a;
$yellow: #c3c320;

.main {
  display: flex;
  align-items: center;
  flex-direction: column;
  color: #fff;

  .lose-window {
    background: rgba(24, 24, 24, 0.85);
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 3rem;
  }

  .game-title {
    margin-top: 0;
    font-size: 3rem;
    letter-spacing: 0.05rem;
  }
  .game-block {
    width: 100%;
    display: flex;
    justify-content: space-between;

    .game-field {
      width: 45%;
      display: flex;
      align-items: center;
      justify-content: flex-end;

      .game-field__item {
        width: 20rem;
        height: 20rem;
        display: flex;
        overflow: hidden;
        border-radius: 50%;
        flex-wrap: wrap;
        border: 8px solid #717171;
        box-sizing: content-box;

        .square {
          width: 50%;
          height: 50%;
          border: none;
          padding: 0;
          cursor: pointer;

          &:disabled {
            transform: none !important;
            border-radius: 0 !important;
            cursor: inherit;
          }

          &:focus {
            outline: none;
          }
        }

        .square_blue {
          background: $blue;
          border-bottom-right-radius: 100%;

          &:hover {
            border-top-left-radius: 0.25rem;
            transform: scale(1.05);
          }

          &.active {
            background: lighten($blue, 20%);
          }
        }

        .square_yellow {
          background: $yellow;
          border-bottom-left-radius: 100%;

          &:hover {
            border-top-right-radius: 0.25rem;
            transform: scale(1.05);
          }

          &.active {
            background: lighten($yellow, 20%);
          }
        }

        .square_red {
          background: $red;
          border-top-right-radius: 100%;

          &:hover {
            border-bottom-left-radius: 0.25rem;
            transform: scale(1.05);
          }

          &.active {
            background: lighten($red, 20%);
          }
        }

        .square_green {
          background: $green;
          border-top-left-radius: 100%;

          &:hover {
            border-bottom-right-radius: 0.25rem;
            transform: scale(1.05);
          }

          &.active {
            background: lighten($green, 20%);
          }
        }
      }

      @media screen and (max-width: 800px) {
        width: fit-content;
      }
    }

    .game-info {
      width: 50%;

      .score {
        margin: 0;
        font-size: 2rem;
        margin-bottom: 0.8rem;
      }

      .start-btn {
        color: #e5ff4b;
        width: 7rem;
        height: 2.5rem;
        border-radius: 10px;
        border: none;
        font-family: 'Roboto';
        background: #cd52cd;
        font-size: 1.2rem;
        cursor: pointer;
      }

      .options {
        font-size: 1.5rem;
      }

      .difficulty-choice {
        .diff-input {
          cursor: pointer;
          width: 0.8rem;
          height: 0.8rem;
        }

        .diff-label {
          cursor: pointer;
          font-size: 1.2rem;
          margin-left: 0.2rem;
        }
      }

      @media screen and (max-width: 800px) {
        flex: 0 1 auto;
        width: fit-content;
        margin-left: 2.5rem;
      }
    }

    @media screen and (max-width: 800px) {
      justify-content: center;
    }
  }
}
</style>
