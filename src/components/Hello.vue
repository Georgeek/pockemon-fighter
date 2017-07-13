<template>
  <div class="hello">
    <section class="row">
        <div class="small-6 columns">
            <h1 class="text-center">Pikachu</h1>
            <div class="healthbar">
                <div
                  class="healthbar text-center"
                  style="background-color: green; margin: 0; color: white;"
                  :style="{width: playerHealth + '%'}" >
                  {{ playerHealth }}
                </div>
            </div>
        </div>
        <div class="small-6 columns">
            <h1 class="text-center">Charmander</h1>
            <div class="healthbar">
                <div
                  class="healthbar text-center"
                  style="background-color: green; margin: 0; color: white;"
                  :style="{width: enemyHealth + '%'}">
                  {{ enemyHealth }}
                </div>
                <!-- Менять цвет при уменьшении HEALTH BAR -->
            </div>
        </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
        <div class="small-12 columns">
            <button id="start-game" @click="startGame">START NEW GAME</button>
        </div>
    </section>
    <section class="row controls" v-else="gameIsRunning">
        <div class="small-12 columns">
            <button id="attack" @click="attack">ATTACK</button>
            <button id="special-attack"@click="specialAttack">SPECIAL ATTACK</button>
            <button id="heal" @click="heal">HEAL</button>
            <button id="give-up" @click="giveUp">GIVE UP</button>
        </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
        <div class="small-12 columns">
            <ul>
                <li v-for="turn in turns"
                    :class="{'player-turn': turn.isPlayer, 'enemy-turn': !turn.isPlayer}"
                >
                  {{ turn.text }}
                </li>
            </ul>
        </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'hello',
  data () {
    return {
      playerHealth: 100,
      enemyHealth: 100,
      gameIsRunning: false,
      turns: []
    }
  },
  methods: {
    startGame() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.enemyHealth = 100;
      this.turns = [];
    },
    attack() {
      let damage = this.calculateDamage(10, 25);
      this.enemyHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: `Пикачу наносит ${damage} урона Чармандеру`
      })
      if (this.checkWin()) { return }

      this.enemyAttacks();
    },
    specialAttack() {
      let damage = this.calculateDamage(20, 35);
      this.enemyHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        text: `Пикачу наносит Чармандеру критические ${damage} урона!!`
      })
      if (this.checkWin()) { return }

      this.enemyAttacks();
    },
    giveUp() {
      this.gameIsRunning = false;
    },
    heal() {
      (this.playerHealth <= 90) ? this.playerHealth += 10 : this.playerHealth = 100;
      this.turns.unshift({
        isPlayer: true,
        text: `Пикачу восстанавливает 10 единиц здоровья!!`
      })
      this.enemyAttacks();
    },
    enemyAttacks() {
      let damage = this.calculateDamage(5, 30);
      this.playerHealth -= damage;
      this.turns.unshift({
        isPlayer: false,
        text: `Чармандер наносит ${damage} урона Пикачу`
      })
      this.checkWin();
    },
    calculateDamage(minDamage, maxDamage) {
      return Math.max(Math.floor(Math.random() * maxDamage) + 1, minDamage);
    },
    checkWin() {
      if (this.enemyHealth <= 0) {
        this.enemyHealth = 0;
        if(confirm('Пикачу победил! Начать заного?')) {
          this.startGame();
        } else {
          this.gameIsRunning = false
        } 
        return true;
      } else if (this.playerHealth <= 0) {
        this.playerHealth = 0;
        if(confirm('Чармандер победил! Начать заного?')) {
          this.startGame();
        } else {
          this.gameIsRunning = false
        } 
        return true;
      }
      return false;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }

  .text-center {
      text-align: center;
  }

  .healthbar {
      width: 80%;
      height: 40px;
      
      background-color: #eee;
      margin: auto;
      transition: width 500ms;
  }
  .healthbar .text-center {
    padding: 7px;
  }

  .controls, .log {
      margin-top: 30px;
      text-align: center;
      padding: 10px;
      border: 1px solid #ccc;
      box-shadow: 0px 3px 6px #ccc;
  }

  .turn {
      margin-top: 20px;
      margin-bottom: 20px;
      font-weight: bold;
      font-size: 22px;
  }

  .log ul {
      list-style: none;
      font-weight: bold;
      text-transform: uppercase;
  }

  .log ul li {
      margin: 5px;
  }

  .log ul .player-turn {
      color: blue;
      background-color: #e4e8ff;
  }

  .log ul .enemy-turn {
      color: red;
      background-color: #ffc0c1;
  }

  button {
      font-size: 20px;
      background-color: #eee;
      padding: 12px;
      box-shadow: 0 1px 1px black;
      margin: 10px;
  }
</style>
