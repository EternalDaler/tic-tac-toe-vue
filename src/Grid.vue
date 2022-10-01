<template>
  <div style="display: block">
    {{current}}
    <div class="steps">
      Ходы: {{count}}
    </div>
    <div class="grid">
      <BoardItem v-for="item in 9" v-bind:id="item" @set-item="moveMy" :ref="`item`"></BoardItem>
      <button v-if="gameover" class="btn" @click="newGame">Новая игра</button>
    </div>
    <div v-if="gameover"> {{game_statement}} </div>
    <div v-if="gameover">
      <div v-if="game_statement!=='Ничья'">
        Выиграл: {{winner}}
      </div>
    </div>
  </div>
</template>

<script>
import BoardItem from "@/BoardItem.vue";

export default {
  name: "Grid",
  components:{
    BoardItem
  },
  setup(){
    return{
      gameconfig:{
        players:{
          player1: {
            id:1,
            symbol: 'X',
            name: 'Вы',
          },
          player2: {
            id:2,
            symbol: 'O',
            name: 'AI'
          }
        },
        game_statement:{
          win: 'Победа',
          loss: 'Поражение',
          draw: 'Поражение'
        }
      },
      grid:[
          0,0,0,
          0,0,0,
          0,0,0
      ],
      current: null,
      gameover: false,
      game_statement: null,
      count: 0,
      winner: null,
    }
  },

  mounted() {
    this.init()
  },

  methods:{
    init(){
      this.current = this.gameconfig.players.player1.name
      this.$forceUpdate()
    },
    newGame(){
      this.grid = [
        0,0,0,
        0,0,0,
        0,0,0
      ]
      this.gameover = false
      for(let i = 0; i < 9; i++){
        this.$refs.item[i].value = null
      }
      this.count = 0
      this.$forceUpdate()
      this.winner = null
      this.current = this.gameconfig.players.player1.name
      this.game_statement = null
    },
    moveBot(){
      let choice = this.getChoice()
      let target = this.$refs.item[choice]
      if(target.value === this.gameconfig.players.player1.symbol || target.value === this.gameconfig.players.player2.symbol){
        return this.moveBot()
      }
      target.value = this.gameconfig.players.player2.symbol
      this.grid[choice] = 2
      this.checkMyWin(2, 'AI')
      this.current='Вы'
      this.count+=1
    },
    moveMy(n){
      // VISUAL

      let target = this.$refs.item[n-1]
      if(target.value || this.current == 'Противник' || this.gameover){return}

      target.value = 'X'

      // GRID
      this.moveMy_Grid(n)

      // OPPONENT MOVE

      if(this.count === 9)
      {
        let myWin = this.checkWin(1)
        // let oppWin = this.checkWin(2)
        if(myWin){
          this.gameover = true
          this.game_statement = 'Победа'
          return;
        }
        this.gameover = true
        this.game_statement = 'Ничья'
        return;
      }

      if(!this.gameover){
        this.moveBot()
      }


    },

    moveMy_Grid(n){
      n = n -1
      this.grid[n] = 1
      this.checkMyWin(1, 'Человек')
      this.current = 'Противник'
      this.count+=1
      this.$forceUpdate()
    },

    getChoice(){
      let bestChoice = Math.floor(Math.random() * 8);
      for(let i = 0; i<9; i++){
        if(this.grid[i] === 2 || this.grid[i] === 1){
          continue
        }
        let simulate = this.simulateStupidHumanMove(i)
        if(simulate){
          bestChoice = i;
          break
        }
      }
      return bestChoice
    },

    simulateStupidHumanMove(n){
      this.grid[n] = 1
      let checkWin = this.checkWin(1)
      this.grid[n] = 0
      return checkWin
    },

    checkWin(player){
      const grid = this.grid
      if(
          // STRAIGHT VERTICAL
          grid[0] === player && grid[3] === player && grid[6] === player ||
          grid[1] === player && grid[4] === player && grid[7] === player ||
          grid[2] === player && grid[5] === player && grid[8] === player ||
          // STRAIGHT HORIZONTAL
          grid[0] === player && grid[1] === player && grid[2] === player ||
          grid[3] === player && grid[4] === player && grid[5] === player ||
          grid[6] === player && grid[7] === player && grid[8] === player ||
          // TILTED
          grid[0] === player && grid[4] === player && grid[8] === player ||
          grid[2] === player && grid[4] === player && grid[6] === player
      ){
        return true
      }
      return false
    },

    checkMyWin(player, winner){
      const grid = this.grid
      if(
          // STRAIGHT VERTICAL
          grid[0] === player && grid[3] === player && grid[6] === player ||
          grid[1] === player && grid[4] === player && grid[7] === player ||
          grid[2] === player && grid[5] === player && grid[8] === player ||
          // STRAIGHT HORIZONTAL
          grid[0] === player && grid[1] === player && grid[2] === player ||
          grid[3] === player && grid[4] === player && grid[5] === player ||
          grid[6] === player && grid[7] === player && grid[8] === player ||
          // TILTED
          grid[0] === player && grid[4] === player && grid[8] === player ||
          grid[2] === player && grid[4] === player && grid[6] === player
      ){
        this.gameover = true
        this.winner = winner
        if(this.winner === 'Человек'){
          this.game_statement = 'Победа'
        }
        if(this.winner === 'AI'){
          this.game_statement = 'Поражение'
        }
      }
    },
    set(row,place){
    }
  }
}
</script>

<style scoped>
.btn{
  font-family: 'Montserrat', sans-serif;
  padding: 20px;
  width: 100%;
  border: none;
  background: #42b983;
  font-size: large;
  color: #fff;
}
.grid{
  width: 330px;
  display: block;
  margin: 0 auto;
}
</style>