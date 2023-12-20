<template>
 <header>
      <h1>Limon the Monster</h1>
    </header>
    <div id="game">
      <!-- monster section -->
      <section id="monster" class="container">  
        <h2>Monster Health</h2>
        <div class="healthbar">

          <!-- change the class style using style class in a dynamic way-->
          <div class="healthbar__value__monster" :style="monsterstyle"></div>
        </div>
      </section>


       <!-- player section -->
      <section id="player" class="container">
        <h2>Your Health</h2>
        <div class="healthbar">
          <!-- change the class style using style class -->
          <div class="healthbar__value__player" :style="playerstyle"></div>
        </div>
      </section>

      <!-- show the result using condition -->
      <section class="container" v-if="winner">
        <h2>Game Over</h2>
        <h2 v-if="winner=='player'">You Win</h2>
        <h2 v-else-if="winner=='monster'">You Lost</h2>
        <h2 v-else>Draw</h2>
        <button @click="start">Start New Game</button>
      </section>

      <!-- funcolatity of my app -->

       <section id="controls" v-else>

        <!-- disabled the button when both of party health are limited -->

        <button @click="attackMonster" :disabled="playerHealth<0 || monsterHealth<0">ATTACK</button>

          <!-- disabled the button when round are not disibled by 3  -->
        <button @click="special" :disabled="currentRound%3!=0||currentRound==0">SPECIAL ATTACK</button>
        
        <button @click="heal" :disabled="playerHealth>=100 || heal_count>=3">HEAL</button>

        <button @click="surrender">SURRENDER</button>
        <button @click="pauseGame">Pause</button>
        <button @click="start" :disabled="is_active">Start new game</button>
      </section>


    <!-- show the result of battle field -->

       <section id="log" class="container">
        <h2 class="b1header">Battle Log</h2>
        <ul>
          <li v-for="logitem in message" :key="logitem" :style="ChangeColor(logitem)">
          {{ logitem.actionby }} -> {{ logitem.actiontype }} -> {{ logitem.actionvalue }}
          </li>
        </ul>
      </section>




  </div>
</template>

<script>
export default {
  name: 'GamePage',
  data(){
    return{
      playerHealth: 100,
      monsterHealth: 100,
      currentRound: 0,
      winner:null,
      heal_count:0,
      message:[],
      is_active:true
    }
  },

  // check frequenly status about player health and monster health

  watch:{

    // check the playervalue
     playerHealth(value){
       if(value<=0&&this.monsterHealth<=0){
       this.winner=draw
       }
       else if(value<=0){
        this.winner="monster"
       }
     },
     monsterHealth(value){
       if(value<=0&&this.playerHealth<=0){
      this.winner="draw"
       }
       else if(value<=0){
        this.winner="player"
       }
     }

  },
  computed:{
    //change the player css style using function value 
   playerstyle(){
    if(this.playerHealth<=0){
      return{width:"0%"}
    }
    return{width:this.playerHealth+'%'}
   },

    //change the monster style using function value 
   monsterstyle(){
    if(this.monsterHealth<=0){
      return{width:"0%"}
    }
    return{width:this.monsterHealth+'%'}
   }
  },
  
   mounted() {
    // Load data from local storage when the component is mounted
    this.loadFromLocalStorage();

   
  },
  
  methods: {

    //save data in local storage

     saveToLocalStorage(){
         localStorage.setItem('log', JSON.stringify(this.message));
      },

      // load the save data from the local storage
     loadFromLocalStorage(){
      const msg=localStorage.getItem("log")
      if(msg){
        this.message=JSON.parse(msg)
      }
     },
     
    getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
    },


    //attack to monster
    attackMonster() {
      //track the round number of game
      this.currentRound++
      const attackValue = this.getRandomValue(5,12)
     
      this.monsterHealth -= attackValue;
      this.logmessage('player','attack',attackValue)

      //when you are attack the monster your life also decraesed to call the attackplayer()
      this.attackPlayer();
    },

    //attack to player
     attackPlayer() {
     const attackValue = this.getRandomValue(5,13)
       this.playerHealth -= attackValue;
       this.logmessage('monster','attack',attackValue)
     },


     //special attack to monster after 3 round
     special(){
      this.currentRound++
      const attackValue=this.getRandomValue(10,25)
      this.monsterHealth-=attackValue;
      this.logmessage('player','attack',attackValue)
      this.attackPlayer();
     },

     //refuel player life 
     heal(){
      // this.currentRound++
      
      const healvalue=this.getRandomValue(2,6)
      if(this.playerHealth+healvalue>=100){
        this.playerHealth=100
      }
      else{
        this.playerHealth+=healvalue
      }
      this.heal_count++
      this.logmessage('player','Heal', healvalue)
      // this.attackPlayer()
     },
     //restart the game 
     start(){
      this.playerHealth=100,
      this.monsterHealth=100,
      this.currentRound=0,
      this.winner=null
      this.heal_count=0
      this.message=[]
      this.is_active=true
     },

     surrender(){
      this.winner="monster"
     },

     pauseGame() {
      // Triggered when the pause button is clicked
      // You can add additional logic here before saving if needed
      this.saveToLocalStorage();
      alert('Game paused. Battle log saved!');
      this.is_active = false
    },

     logmessage(who,what,value){

           this.message.unshift({
            actionby:who,
            actiontype:what,
            actionvalue:value
           })
     },
     ChangeColor(logitem){
    
      if(logitem.actionby=="player"){
       return{color:"red"}
      }
      else{
      return{color:"green"}
      }
      return color
     }

  }
}
</script>




<style scoped>
header {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  padding: 0.5rem;
  background-color: #880017;
  color: white;
  text-align: center;
  margin-bottom: 2rem;
}

section {
  width: 90%;
  max-width: 40rem;
  margin: auto;
}

.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background: #fde5e5;
}

.healthbar__value__monster {
  background-color: blueviolet;
  width: 100%;
  height: 100%;
}
.healthbar__value__player {
  background-color:greenyellow;
  width: 100%;
  height: 100%;
}

.container {
  text-align: center;
  padding: 0.5rem;
  margin: 1rem auto;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  border-radius: 12px;
  color:red
}

#monster h2,
#player h2 {
  margin: 0.25rem;
}

#controls {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

button {
  font: inherit;
  border: 1px solid #88005b;
  background-color: #88005b;
  color: white;
  padding: 1rem 2rem;
  padding: 1rem 2.5rem;
  border-radius: 12px;
  margin: 1rem;
  width: 14rem;
  cursor: pointer;
  box-shadow: 1px 1px 4px rgba(0, 0, 0, 0.26);
}

button:focus {
  outline: none;
}

button:hover,
button:active {
  background-color: #af0a78;
  border-color: #af0a78;
  box-shadow: 1px 1px 8px rgba(0, 0, 0, 0.26);
}

button:disabled {
  background-color: #ccc;
  border-color: #ccc;
  box-shadow: none;
  color: #3f3f3f;
  cursor: not-allowed;
}

#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
}

#log li {
  margin: 0.5rem 0;
}

.log--player {
  color: #7700ff;
}

.log--monster {
  color: #da8d00;
}

.log--damage {
  color: red;
}

.log--heal {
  color: green;
}
.b1header{
  color: blue;
}
</style>
