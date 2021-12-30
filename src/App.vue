<template>
<div class="wrapper">
  <div class="gameBody">
    <div class="menu">
      <button :class="{active: activeBtn === 'btn1'}" @click="difficultyLevel = 'easy'; activeBtn = 'btn1'">easy</button>
      <button :class="{active: activeBtn === 'btn2'}" @click="difficultyLevel = 'normal'; activeBtn = 'btn2'">normal</button>
      <button :class="{active: activeBtn === 'btn3'}" @click="difficultyLevel = 'hard'; activeBtn = 'btn3'">hard</button>
      <button class="startBtn" @click="runGame">{{runResBtn}}</button>

    </div>
    <div class="scoreBoard"><div>{{score}}</div></div>
    <div class="game">
      <div class="square" @click="clicked(1)" :class="{lit: isLit[1]}"></div>
      <div class="square" @click="clicked(2)" :class="{lit: isLit[2]}"></div>
      <div class="square" @click="clicked(3)" :class="{lit: isLit[3]}"></div>
      <div class="square" @click="clicked(4)" :class="{lit: isLit[4]}"></div>
    </div>
  </div>
</div>
</template>

<script>


export default {
  name: 'App',
  data(){
    return{
      runResBtn: 'Run Game',
      isClickable: false,
      running: false,
      sequence: [],
      sequenceInterval: null,
      playerSequence: [],
      score: 0,
      isWon: false,
      isWrong: false,
      activeBtn: 'btn1',
      difficultyLevel: 'easy',
      isLit: {
        1: false,
        2: false,
        3: false,
        4: false
      },
      sounds:{
        1:'sounds/simonSound1.mp3',
        2:'sounds/simonSound2.mp3',
        3:'sounds/simonSound3.mp3',
        4:'sounds/simonSound4.mp3',
      },
    }
  },
  methods:{
    clicked(tile) {
      if (this.isClickable) {
        this.playSound(tile)
        this.lightUp(tile)
        this.playerSequence.push(tile)
        clearInterval(this.sequenceInterval)
        this.checkSequence()
      }
      },
    runGame(){
      this.running = true
      this.runResBtn = "Reset"
      this.sequence = []
      this.playerSequence = []
      this.isWon = false
      this.isWrong = false
      this.score = 0
      clearInterval(this.sequenceInterval)
      this.showSequence()
    },
    playSound(tile){
      if(this.sounds[tile]){
        let audio = new Audio(this.sounds[tile])
        audio.play()
      }
    },
    lightUp(tile) {
      this.isLit[tile] = true
      setTimeout(() => {
        this.isLit[tile] = false
      }, 250);
    },
    difficulty(level) {
      if (level === 'easy') {
        return 1500
      }
      else if (level === 'normal') {
        return 1000
      }
      else {
        return 400
      }
    },
    showSequence(redo) {
      let currentIndex = 0
      let speed = this.sequence.length === 0 ? 1000 : this.difficulty(this.difficultyLevel)
      this.isClickable = false
      if (!redo) {
        this.sequence.push(Math.floor(Math.random() * 4 + 1))
      }
      this.sequenceInterval = setInterval(() => {
        if (currentIndex >= this.sequence.length) {
          clearInterval(this.sequenceInterval)
          return (this.isClickable = true)
        }
        this.playSound(this.sequence[currentIndex])
        this.lightUp(this.sequence[currentIndex])
        currentIndex++
      }, speed)
    },
    checkSequence(){
      for (let i = 0; i < this.playerSequence.length; i++) {
        if (this.playerSequence[i] !== this.sequence[i]) {
          this.playerSequence = [];
          this.isWrong = true;
          this.isClickable = false;
          this.score='You lose!!!'
          setTimeout(() => {
            this.runGame()
          }, 2000)
        }
      }

      if (this.playerSequence.length === this.sequence.length) {
        this.playerSequence = [];
        this.score++;

        if (this.score === 5) {
          this.score = "You win!!!";
          this.runResBtn = 'New Game'
          this.isClickable = false;
          this.isWon = true;
        } else {
          this.showSequence();
        }
      }
    },
  },
}
</script>
