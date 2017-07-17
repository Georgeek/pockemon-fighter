<template>
  <div class="hello">
    <div class="container">
      <div class="battleground-background"></div>
      <!-- Внести Покемонов в 1 блок и задать ему фон полосками и градиентом
          добавить поляны каждому из Покемонов -->
      <section class="row row--margin-bottom">
        <div class="small-6 columns">
          <div class="player__block">
            <div class="player__info">
              <h1 class="text-center">Charmander</h1>
              <div class="healthbar">
                  <div class="healthbar__text">HP</div>
                  <div class="healthbar__bar">
                    <div
                      class="healthbar__bar--health text-center"
                      style="margin: 0; color: white;"
                      :style="{width: enemyHealth + '%'}"
                      :class="{'green': fullHealthEnemy(), 'orange': underSixtyHealthEnemy(), 'red': underThirtyHealthEnemy()}">
                    </div>
                  </div>
              </div>
            </div>

            <div class="player__block--shadow shadow--right"></div>
          </div>
        </div>

        <div class="small-6 columns">
          <img src="../assets/charmander.png">
          <div class="grass"></div>
        </div>
      </section>

      <section class="row">
        <div class="small-6 columns">

          <div class="grass"></div>
          <div class="player">
            <div class="pikachu"></div>
          </div>
        </div>

        <div class="small-6 columns">
          <div class="player__block">
            <div class="player__info">
              <h1 class="text-center">Pikachu</h1>
              <div class="healthbar">
                  <div class="healthbar__text">HP</div>
                  <div class="healthbar__bar">
                    <div
                      class="healthbar__bar--health text-center"
                      style="margin: 0; color: white;"
                      :style="{width: playerHealth + '%'}"
                      :class="{'green': fullHealthPlayer(), 'orange': underSixtyHealthPlayer(), 'red': underThirtyHealthPlayer()}">
                      
                    </div>
                  </div>
              </div>
              <div class="player__healthbar">
                {{ playerHealth }} / 100
              </div>
            </div>

            <div class="player__block--shadow shadow--left"></div>
          </div>

        </div>
      </section>
      
      <section class="row controls" v-if="!gameIsRunning">
          <div class="small-12 columns">
              <button id="start-game" @click="startGame">НАЧАТЬ БОЙ</button>
          </div>
      </section>
      <section class="row controls" v-else="gameIsRunning">
          <div class="small-6 flex">
            <div class="pokemon-question">
              Что сделать <span class="pokemon-name"> Пикачу</span> ?
            </div> 
          </div>
          
          <!-- Отобразить кнопки Ровно. Вопрос про Пикачу выровнять по центру. Оформить в стиле картинки
          https://www.gamerstemple.net/vg/games11/001336/001336s01.jpg 
          При наведении мышкой показывать стрелочку?? Подумать над этим -->
          <div class="small-6 columns">
            <div class="pokemon-actions">
              <button id="attack" @click="attack">АТТАКА</button>
              <button id="special-attack"@click="specialAttack">УДАР МОЛНИЕЙ</button>
              <button id="heal" @click="heal">ЛЕЧЕНИЕ</button>
              <button id="give-up" @click="giveUp">СДАТЬСЯ</button>
            </div>
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
    },
    underSixtyHealthPlayer() {
      return this.playerHealth < 60 && this.playerHealth >= 30;
    },
    underThirtyHealthPlayer() {
      return this.playerHealth < 30;
    },
    fullHealthPlayer() {
      return this.playerHealth >= 60;
    },
    underSixtyHealthEnemy() {
      return this.enemyHealth < 60 && this.enemyHealth >= 30;
    },
    underThirtyHealthEnemy() {
      return this.enemyHealth < 30;
    },
    fullHealthEnemy() {
      return this.enemyHealth >= 60;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container {
    max-width: 62.5rem;
    margin: 0 auto;
    padding-top: 50px;
    border-radius: 5px;
    position: relative;
  }
  .row--margin-bottom {
    margin-bottom: 40px;
  }
  .flex {
    display: flex;
  }
  .healthbar {
    align-self: flex-end;
    align-items: center;
    background-color: #686868;
    border-radius: 16px;
    display: flex;
    padding-left: 10px;
    padding-right: 2px;
    width: 80%;
  }
  .healthbar__text {
    background: -webkit-linear-gradient(#fffb26, #f9b60c);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    font-weight: bold;
    font-size: 2em;
    line-height: 1em;
    margin: 0 2px;
  }
  .healthbar__bar {
    border: 5px solid white;
    border-radius: 20px;
    width: 100%;
  }
  .healthbar__bar--health {
    border-radius: inherit;
    transition: width 500ms;
  }
  .healthbar .text-center {
    padding: 7px;
  }

  .player__block {
    position: relative;
  }
  .player__block--shadow {
    background: #686868;
    bottom: -15px;
    height: 80px;
    position: absolute;
    width: 90%;
    z-index: -1;
  }
  .player {
    display: flex;
    justify-content: space-around;
  }
  .player__info {
    background: #fcf1de;
    border: 5px solid #686868;
    border-radius: 25px 10px;
    box-shadow: inset 0 3px 10px #ccc;
    display: flex;
    flex-direction: column;
    padding: 5px 20px;
    width: 100%;
  }
  .player__healthbar {
    align-self: flex-end;
    font-size: 2em;
  }

  .shadow--left {
    border-radius: 0 10px 25px 0;
    left: 58px;
  }
  .shadow--right {
    border-radius: 0 0 0 25px;
    left: 35px;
  }
  .shadow--left:before {
    border-top: 80px solid transparent;
    border-right: 130px solid #686868;
    bottom: 0;
    content: '';
    left: -130px;
    position: absolute;
  }
  .shadow--right:before {
    border-top: 80px solid transparent;
    border-left: 130px solid #686868;
    bottom: 0;
    content: '';
    position: absolute;
    right: -60px;
  }

  .enemy {
    display: flex;
    justify-content: space-around;
  }
  .enemy__info {
    display: flex;
    flex-direction: column;
    width: 100%;
  }
  img {
    height: 163px;
  }

  .hello{
    margin-top: 50px;
  }

  h1, h2 {
    align-self: flex-start;
    font-weight: normal;
    text-shadow: 3px 3px 3px #ccc;
    margin: 0;
  }
  .pokemon-question {
    font-size: 2rem;
    background: #285069;
    border: 10px solid #e2bb2d;
    border-radius: 10px;
    box-shadow: inset 0px 0px 0px 5px #fbe0ff;
    color: white;
    flex: 1;
    padding-top: 2rem;
    text-shadow: 2px 2px 1px #000;
  }
  .pokemon-name {
    text-transform: uppercase;
  }
  .pokemon-actions {
    background: #f8f8f8;
    border: 10px solid #868BBD;
    border-radius: 10px;
    box-shadow: inset 0px 0px 0px 5px #6F6B7E;
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
  .pikachu {
    background: url(../assets/pokemon_back.png);
    height: 163px;
    width: 172px;
    position: relative;
    bottom: -3px;
  }
  .grass {
    border-radius: 50%;
    height: 120px;
    width: 420px;
    background: #9bff9d;
    position: absolute;
    bottom: -70px;
    left: 40px;
    z-index: -1;
    border: 10px solid rgba(0, 0, 0, 0.1);
  }
  .battleground-background {
    background: linear-gradient(
                      to bottom,
                      #68d9ff 10%,
                      #9effa2,
                      #e0ffe3
                    );
    z-index: -1;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
  }
  .controls, .log {
    border: 7px solid #424242;
    box-shadow: 0px 3px 6px #424242;
    text-align: center;
    background: #424242;
    display: flex;
  }
  .turn {
    font-size: 22px;
    font-weight: bold;
    margin-bottom: 20px;
    margin-top: 20px;
  }
  .log ul {
    font-weight: bold;
    list-style: none;
    text-transform: uppercase;
  }
  .log ul li {
    margin: 5px;
  }
  .log ul .player-turn {
    background-color: #e4e8ff;
    color: blue;
  }
  .log ul .enemy-turn {
    background-color: #ffc0c1;
    color: red;
  }
  button {
    background-color: #eee;
    box-shadow: 0 1px 1px black;
    font-size: 20px;
    padding: 12px;
    margin: 10px;
  }
  .green {
    background: #9bf7ac;
  }
  .red {
    background: red;
  }
  .orange {
    background: orange;
  }
  .small-6 {
    position: relative;
  }
</style>
