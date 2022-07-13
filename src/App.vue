<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <html>
    <head>
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.13.0/css/all.min.css">
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
      <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">

    </head>
    <body>
      <div class="wrapper">

        <div class="detail">
          <div>
            <div class="button" @click="reset()">RESET</div>
            <!-- <br><br><br>
            <div class="button" @click="spectate = !spectate">spactate</div> -->
          </div>

          <div style=" margin-left:20px;">
            <i class="fa fa-clock-o" style="font-size:125%;margin-right:5px"></i><span>{{showingTimer}}:</span>
            &nbsp;
            <span style="margin-top:50px">Move Count: {{moveCount}}</span>
            <!-- <span @click="spectate = !spectate" style="background-color: aqua; ">:{{hasSelectedCard}}:</span> -->
          </div>
        </div>

        <div class="top-section">
          <div class="finished-area" v-if="hasSelectedCard" @click="finishDrop()"></div>


        
          <div class="child" :class="heartList.length==0? 'empty' : ''">
            <template v-for="(card,index) in heartList" :key="index" >
              <div v-if="index == heartList.length-1" class="front"   @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" >
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="spadeList.length==0? 'empty' : ''">
            <template v-for="(card,index) in spadeList" :key="index">
              <div v-if="index == spadeList.length-1" class="front"   @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']">
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="diamondList.length==0? 'empty' : ''" >
            <template v-for="(card,index) in diamondList" :key="index">
              <div v-if="index == diamondList.length-1" class="front"  @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']">
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" style="margin-right: 33px" :class="clubList.length==0? 'empty' : ''" >
            <template v-for="(card,index) in clubList" :key="index">
              <div v-if="index == clubList.length-1" class="front"   @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']">
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>



          <div class="child" :class="sideList.length==0? 'empty' : ''" style="margin-right: 30px">
            <template v-for="(card,index) in sideList" :key="index">
              <div v-if="index > sideList.length-4 && sideList.length == index+1" class="front"  draggable @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" :style="judgeForSide(index)? '':'margin-top:-40px'">
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="" v-if="isFinalCard(index,'side')">
              </div>

              <div v-if="index > sideList.length-4 && sideList.length !== index+1" class="front" :class="[card.selected ? 'card-selected' : 'card-not-selected']" :style="judgeForSide(index)? '':'margin-top:-40px'">
                <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                <!-- <span :style="{'color':card.color}">{{index}}</span> -->
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="" v-if="isFinalCard(index,'side')">
              </div>
              
            </template>
          </div> 

          <div class="child " :class="deckNum == 0? 'empty' : ''" style="margin-right: -6px" @click="startGame()">

            <div v-if="deckNum !==0 && hasGameStarted" class='back'></div>
            <div v-if="deckNum ==0 && hasGameStarted"> <img style="margin-top:20px;" src="../public/iconmonstr-refresh-1.svg" ></div>
            
            <div v-if="!hasGameStarted" class="start"><span>START</span> </div>
            
            

          </div>
          <span style="margin-top: 62px; margin-left:-25px; color:FloralWhite; font-weight:bold;">{{deckNum}}</span>

        </div>

        <div class="bottom-section" >

          <!-- <template v-for="(row,rowIndex) in rows" :key="rowIndex" >
            <div v-if="rowIndex < 7"  >
              <div v-if="row.length == 0" class="empty child" @click="toEmptySpace(rowIndex)"></div>

              
              <div v-else class="child" style="position:absolute; left: 100px;" >

                <template v-for="(card,index) in row" :key="index"  >
                  <div style="right:100px;">
                     <div v-if="!card.isOpened" class="back"  @click="cardClick(card,'pick')"  >
                      </div>
      
                      <div v-else class="front"  
                      @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" >
                        <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                        <img class="smallImage" :src="getSVG(card.kind)" alt="">
                        <br>
                        <img v-if="isFinalCard(index,rowIndex)" class="bigImage" :src="getSVG(card.kind)" alt="">
                      </div>
                  </div>
                  <template> -->

                  
  
                    <!-- <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-45px'" @click="cardClick(card,'pick')" >
                    </div>
    
                    <div v-else class="front" :style="index==0? '':'margin-top:-43px'" @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" >
                      <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                      <img class="smallImage" :src="getSVG(card.kind)" alt="">
                      <br>
                      <img v-if="isFinalCard(index,rowIndex)" class="bigImage" :src="getSVG(card.kind)" alt="">
                    </div> -->

                   

                  <!-- </template> 
  
                </template>

                
              </div>
            </div>

          </template> -->



          <!-- this for empty space to put it back -->
          <template v-for="(row,rowIndex) in rows" :key="rowIndex" >

            <div v-if="rowIndex < 7 "  :style="{'left': rowIndex * 51  + 'px'}" style="position: absolute; width: 40px; " >
              <div v-if="row.length == 0" class="empty child" @click="toEmptySpace(rowIndex)"></div>
            </div>

          </template> 

          <div v-if="hasGameStarted">
            <template  v-for="(card,index) in deckDetail" :key="index" >


              <div style="position: absolute; width: 43px; transition : all 0.6s ease 0s;" :style="{'top': card.y * 23  + 'px', 'left': card.x * 51  + 'px','z-index' : getZ(card)}" v-if="card.location == 'field'" :key="card">

                  <div v-if="!card.isOpened" class="back" @click="cardClick(card,'pick')"   >
    
                  </div>
    
                  <div v-else class="front"  @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" >
                    <span :style="{'color':card.color}" >{{convertNum(card.num)}}</span>
                    <!-- <span :style="{'color':card.color}" v-else>{{card.movingNow}}</span> -->
                    <!-- <span :style="{'color':card.color}" style="font-size: 50%;">{{card.movingNow}}</span> -->
                    <img class="smallImage" :src="getSVG(card.kind)" alt="">
                    <br>
                    <!-- <img v-if="isFinalCard(card.y,card.x,card.movingNow)" class="bigImage" :src="getSVG(card.kind)" alt=""> -->
                    <img class="bigImage" :src="getSVG(card.kind)" alt="">
                  </div>
              </div>
              

            </template>
          </div>




        

        </div>

        <div class="undo-section" @click="allUnselected()">

        </div>


      </div>
    </body>
     
  </html>
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
</template>

<script>
import heartImage from "../public/heart-svgrepo-com.svg"
import clubImage from "../public/clover-black-shape-svgrepo-com.svg"
import spadeImage from "../public/spades-svgrepo-com.svg"
import diamondImage from "../public/diamond-svgrepo-com.svg"

// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld
  },
  data(){
    return{
      isGameOver: false,
      deckDetail: undefined,
      hasGameStarted: false,
      sideCount: 0,
      isMultiple: false,
      isMixedOver: false,

      shuffledIndex: [],

      moveCount: 0,
      showingTimer: '00:00',
      second: 0,
      minute: 0,
      hours: 0, 

      hasSelectedCard: false,
      currentCardId: undefined,


      spectate: false,

      shuffle_audio: new Audio(require('@/assets/sounds/shuffle_sound.wav')),
      move_audio: new Audio(require('@/assets/sounds/move_card.wav')),
      deal_auido: new Audio(require('@/assets/sounds/deal3.wav')),
      // My Movie 1.m4a

      // movingSpeed: 0.2,
    }
  },

  methods:{

    startCounting(){
      
      if(this.hours < 2) {
        setTimeout(() => {
          this.second++
          if(this.second == 60){
            this.second = 0
            this.minute++
          }
          if(this.minute == 60){
            this.minute =0
            this.hours++ 
          }
          if(this.minute < 10){
            this.showingTimer = `${this.hours}:0${this.minute}`
          }else{
            this.showingTimer = `${this.hours}:${this.minute}`
          }
          if(this.second < 10){
            this.showingTimer = `${this.showingTimer}:0${this.second}`
          }else{
            this.showingTimer = `${this.showingTimer}:${this.second}`
          }
          // this.showingTimer = `${this.hours}:${this.minute}:${this.second}`
          this.startCounting()
        }, 1000)
      }
    },
    startGame(){
      if(this.hasGameStarted){
        this.openMoreCard()
        return
      }
      this.startCounting()
      this.hasGameStarted = true
      console.log('starting game')
      let count = 0
      let horizontal =0
      let vertical = 0

      while(count<28){
        let randomNum =Math.floor(Math.random() * 51);
        if(this.deckDetail[randomNum].location == 'deck'){
          this.deckDetail[randomNum].location = 'field'
          this.deckDetail[randomNum].x = horizontal
          this.deckDetail[randomNum].y = vertical
          vertical++

          if(horizontal== 0 &&  vertical ==1){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 1 && vertical ==2){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 2 && vertical ==3){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 3 && vertical ==4){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 4 && vertical ==5){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 5  && vertical ==6){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 6  && vertical ==7){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }


          
          count++
        }
      }
      // var shuffle_audio = new shuffle_audio('../public/shuffle_audio/shuffle_sound.wav');
      this.shuffle_audio.play();


      this.test()

    },

    openMoreCard(){
      this.allUnselected()
      // if it is A or the ones following then put it into the corner

      // reset cards to decks
      if(this.deckNum ==0){
        this.isMixedOver = true
        for (let i in this.deckDetail){
          if(this.deckDetail[i].location == 'side'){
            this.deckDetail[i].location = 'deck'
            this.deckDetail[i].sideCount = 0
          }
        }
        return 
      }

      if(this.isMixedOver){
        this.deal_auido.pause()
        this.deal_auido.play()
        for(let i in this.shuffledIndex){
          let theIndex = this.shuffledIndex[i]
          if(this.deckDetail[theIndex].location == 'deck'){
            this.deckDetail[theIndex].location = 'side'
            this.deckDetail[theIndex].sideCount= this.sideCount
            this.deckDetail[theIndex].x = 11
            this.sideCount++
            this.moveCount++
            return
          }
        }
      }

      let flag = false
      while(!flag){
        let randomNum =Math.floor(Math.random() * 52);
        if(this.deckDetail[randomNum].location == 'deck'){
          this.shuffledIndex.push(randomNum)

          this.deckDetail[randomNum].location = 'side'
          this.deckDetail[randomNum].sideCount= this.sideCount
          this.deckDetail[randomNum].x = 11
          this.sideCount++
          flag =true
          this.moveCount++
        }
      }
      this.deal_auido.pause()
      this.deal_auido.play()
    },

    test(){
    },

    reset(){
    if (!confirm("Would you like to reset the whole game?") ) return

    this.isGameOver=  false,
    this.deckDetail =  undefined
    this.hasGameStarted= false
    this.sideCount=0
    this.isMultiple=false
    this.isMixedOver= false

    this.shuffledIndex=[]

    this.moveCount= 0
    this.showingTimer= '00:00'
    this.second= 0
    this.minute= 0
    this.hours=0 
    this.spectate = false

    // this.startCounting()
    let count =0
    let kind = 'club'
    let num = 1
    let color = 'black'
    
    this.deckDetail =[]
    while(count<52){

      this.deckDetail.push({kind: kind,num: num,location: 'deck',x: 12, y: undefined, isOpened: false,color:color,cardId: `${kind}-${num}`})

      if(num ==13){
        num = 0
      }

      if(count ==12){
        
        kind = 'spade'
      }else if(count == 25){
        color = 'red'
        kind = 'heart'
      }else if(count == 38 ){
        kind = 'diamond'
      }

      count++
      num++
    
    }

      
        
      
    },

    getSVG(kind){
      switch(kind){
        case 'heart':
          return heartImage
        
        case 'club':
          return clubImage

        case 'spade':
          return spadeImage

        case 'diamond':
          return diamondImage

        default:
          return
      }
    },

    isFinalCard(index,row,movingNow){
      // console.slog(index)
      
      if(row == 'side'){
        let lastIndex = this.sideList.length
        
        if(index+1 == lastIndex){
          return true
        }else{
          return false
        }
      }
      console.log(movingNow)

      if(movingNow) return true
      // what if it is multile 
      
      let lastIndex = this.rows[row].length
      if(index+1 == lastIndex){
        return true
      }else{
        return false
      }
    },

    judgeForSide(index){
      let theLength = this.sideList.length
      if(theLength == 1 || theLength == 2 ){
        if(index == 0){
          return true
        }else{
          return false
        }
      }

      if(index == this.sideList.length -3){
        return true
      }else{
        return false
      }
    },

    
    

    finishDrop () {
      // console.log('finish-drop')
      if(!this.hasSelectedCard){
        this.allUnselected()
        return
      }
      this.allUnselected()
      this.hasSelectedCard = false
        
      
      
      const cardId = this.currentCardId
      const card = this.deckDetail.find(o => o.cardId === cardId)
      let previousLocation = card.location 
      let row = card.x
      let flag = true

      // only if this is the last card of the row
      if(card.location == 'finished') return
      
      if(card.location == 'field'){
        if(cardId !== this.rows[row][this.rows[row].length-1].cardId) return 
      }

      // let bottomCard = this.rows[row][this.rows[row].length-1]


      // dropable judge ing
      this.moveCount++
      
      switch(card.kind){
        case 'heart':
          
          if(this.heartList.length == card.num-1){
            card.location = 'finished'
            card.x = undefined
            flag = false
          }
          break;

        case 'spade':
          
          if(this.spadeList.length == card.num-1){
            card.location = 'finished'
            card.x = undefined
            flag = false
          }
          break;
        
        case 'diamond':
          
          if(this.diamondList.length == card.num-1){
            card.location = 'finished'
            card.x = undefined
            flag = false
          }
          break;
        
        case 'club':
          
          if(this.clubList.length == card.num-1){
            card.location = 'finished'
            card.x = undefined
            flag = false
          }
          break;
      }
      

      // flip card once it is gone

      if(previousLocation == 'side'){
        flag =false
      }else{
        flag= true
      }

      if(flag){
        if(card.y !==0){

          
          // console.log('this function called')
          let newID = this.rows[row][this.rows[row].length-1].cardId
          const leftCard = this.deckDetail.find(o => o.cardId === newID)
          // console.log(`left card is: ${leftCard.cardId}`)
  
          leftCard.isOpened = true
        }
      }
      


      
      
            
    },

    convertNum(num){
      if(num == 11){
        return 'J'
      }else if(num == 12){
        return 'Q'
      }else if(num == 13){
        return 'K'
      }else{
        return num
      }
    },

    cardClick(card){
      if((this.spectate || !card.isOpened) && card.location == 'field' ){
        console.log(`specysyr card is: ${card.cardId}, x:${card.x}, y:${card.y}`)
        return 
      }
      
    
      if(!this.hasSelectedCard){
        if(!card){
          return
        }
        // if(card.location )

        this.currentCardId = card.cardId
        let row = card.x
        let height = card.y
        if(card.location == 'field'){
          if(card.cardId == this.rows[row][this.rows[row].length-1].cardId ){
            this.isMultiple = false
            
          }else{
            this.isMultiple = true
            for(let i in this.rows[row]){
              if(this.rows[row][i].y > height){
                this.rows[row][i].selected= true
              }
            }

            
          }
        }else{
          this.isMultiple = false
        }
        
        // evt.dataTransfer.dropEffect = 'move'
        // evt.dataTransfer.effectAllowed = 'move'
        this.currentCardId = card.cardId
        // evt.dataTransfer.setData('cardId', card.cardId)
        // check if it is the final card of the low or not

        // toggle 
        let currentCard = this.deckDetail.find(o => o.cardId === card.cardId)
        currentCard.selected= true
        
        this.hasSelectedCard = true







        // --------------------------------------------
        

      }else{


        
        const current = this.deckDetail.find(o => o.cardId === card.cardId)
        const previous = this.deckDetail.find(o => o.cardId === this.currentCardId)
        // console.log(`card: ${previous.cardId}}`)

        if(current.location !== 'field') {
          this.hasSelectedCard = false
          this.allUnselected()
          return
        }
        

        // dropable judge ing
        // console.log(this.rows[current.x])
        if(!this.rows[current.x].length ==0){
          if(previous.color == current.color){
            this.hasSelectedCard = false
            this.allUnselected() 
            return 
          }

          if(previous.num !== current.num -1){
            this.hasSelectedCard = false
            this.allUnselected()
            return
          }

        }
        


        


        // flip card once it is gone
        if(previous.y !==0 && previous.location =='field'){
            let newID = this.rows[previous.x][previous.y-1].cardId
            const leftCard = this.deckDetail.find(o => o.cardId === newID)
            // console.log(`left card is: ${leftCard.cardId}`)
    
            leftCard.isOpened = true

          
          // console.log('this function called')
        }



        // changin the location for the movwd card
        let count = previous.y
        let list = []
        this.moveCount++
        
        if(this.isMultiple){
          // console.log(`not multiple`)
          while (count < this.rows[previous.x].length ){
            // console.log(this.rows[previousX][count].cardId)
            list.push(this.rows[previous.x][count])
            
            count++
          }

            
          for (let i in list){
            list.movingNow = true
            
            list[i].x = current.x

            if(this.rows[previous.x].length ==0){
              list[i].y =0
            }else{
              list[i].y = this.rows[previous.x].length -1
            }
          }

          this.allUnselected()

          setTimeout( () => {
          this.stopEverything()

        }, 500);


        }else{


          // right here ------------------------------------------------------------
          // changing x location
          previous.x = current.x

          // const movingSpeed = 0.05
          // const basedSpeed = 4

          // previous.movingNow = true
          // let og = previous.x
          // let goal = current.x
          // let diff =Math.abs(og- goal)
          // console.log(`diff is: ${diff}`)

          // previous.y = 10
          
          // count = 0 
          // let limit = 500
          // while(count < limit){
          //   if (og < goal) {
          //     setTimeout(
          //     function() {
          //       og = og + diff/limit
          //       previous.x = og
          //       console.log('x to right')
          //     }, basedSpeed);
          //   }else{
          //     setTimeout(
          //     function() {
          //       og = og - diff/limit
          //       previous.x = og
          //       console.log('x to right')
          //     }, basedSpeed);
          //   }

          //   count++
          // }

          // count should be 1000 in 1 second
          // 1000 = 1 tile
            // 1000 distance with 1000 moves=  1
            // 2000 distance with 1000 moves=  2
            // 3000 distance with 1000 moves=  3

          // 2 then 
          // 3 is 3000



          // let xFlag = false

          // finish the move just for 1 seecond 
          

          // moving x----------
            // setInterval(function(){
            //   if(!xFlag){
            //     if(og+0.05 > goal && og- 0.05 < goal){
            //       xFlag= true
            //       console.log(xFlag)
            //     }else if (og <= goal) {
            //       og = og + movingSpeed
            //       previous.x = og
            //       console.log('x to right')
            //     }else if (og >= goal) {
            //       og = og - movingSpeed
            //       previous.x = og
            //       console.log('x to left')
            //     }
            //   }
            // } ,basedSpeed);


        
          // moving y ----------------
          // previous.isOpened = true  
          // previous.location = 'field'


          // // moving to empty one 
          // if(this.rows[current.x].length ==0){
          //   og = previous.y
          //   goal = 0
          //   if(og < goal){
              
          //     console.log('here 2')


          //     setInterval(function(){
          //       if (og == goal) {
          //         og = og - movingSpeed
          //         previous.y = og
          //         console.log('moving it now')
          //       }
          //     } ,5);
          //   }

          //   // previous.y = 0
          // }else{
          //   // moving to existed row------------
          //   og = previous.y
          //   goal = current.y + 1

          //   if(og < goal){
              
          //     console.log('moving to lower one')


          //     setInterval(function(){
          //       if (og < goal) {
          //         og = og - movingSpeed
          //         previous.y = og
          //         console.log('moving it now')
          //       }
          //     } ,5);

          //   }else if(og > goal){
              
          //     console.log('moving to up')


          //     setInterval(function(){
          //       if (og > goal) {
          //         og = og + movingSpeed
          //         previous.y = og
          //         console.log('moving it now')
          //       }
          //     } ,5);
            
          //   }
          //   // previous.y = current.y + 1

          // }
          previous.y = current.y + 1
        }

        
        
        this.hasSelectedCard = false
        previous.movingNow = true

        this.deal_auido.pause()
        this.deal_auido.play()
        
        this.allUnselected()
        // setTimeout(() => this.basketAddSuccess = false, 2000);
        setTimeout( () => {
          this.stopEverything()

        }, 500);

        
    
      }
    },

    allUnselected(){
      for(let i in this.deckDetail){
        this.deckDetail[i].selected = false
      }
    },

    stopEverything(){
      for(let i in this.deckDetail){
        this.deckDetail[i].movingNow = false
      }
    },

    toEmptySpace(newRow){
      // console.log('dropping to empty space')
      const previous = this.deckDetail.find(o => o.cardId === this.currentCardId)
      
      // judging
      if(previous.num !==  13){
        this.hasSelectedCard = false
        this.allUnselected()
        return
      }
      // flip card once it is gone
      if(previous.y !==0 && previous.location =='field'){
        let newID = this.rows[previous.x][previous.y-1].cardId
        const leftCard = this.deckDetail.find(o => o.cardId === newID)
        leftCard.isOpened = true
      }
      // changin the location for the movwd card
      let count = previous.y
      let list = []
      this.moveCount++
      
      if(this.isMultiple){
        while (count < this.rows[previous.x].length ){
          list.push(this.rows[previous.x][count])
          count++
        }
          
        for (let i in list){
          
          list[i].x = newRow
          if(this.rows[newRow].length ==0){
            list[i].y =0
          }else{
            list[i].y = this.rows[newRow].length -1
          }
        }
      }else{
        // console.log(`at least here`)
        
        previous.isOpened = true
        previous.location = 'field'
        // console.log(`new row's length is: ${this.rows[newRow].length}`)
        // console.log(`new row's length is: ${this.rows[newRow]}`)
        if(this.rows[newRow].length ==0){
          previous.y = 0
        }else{
          previous.y = this.rows[newRow].length + 1
          console.log(`y is: ${this.rows[newRow].y}`)
        
        }
        previous.x = newRow
      }
      // let newCard = this.deckDetail.find(o => o.cardId === previous.cardId)
      // newCard = previous
      // console.log(newCard)
      // dropping effect
      this.hasSelectedCard = false
      this.allUnselected()
      // set  all selected false
    },

    getTop(){
      // return `${100 + height * 5}px`
      return `0px`
    },
    getLeft(left){
      return `${100 + left * 5}px`
    },

    getZ(card){
      if(!card.movingNow ) return card.y

      return 100+ card.y
    },



  },

  computed:{

    fieldsCards: function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'field'){
          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.y) - parseFloat(b.y);
        // return parseFloat(a.x) - parseFloat(b.x);
        

      });
      return list
    },

    rows: function(){
      let baseList = []



      let count = 0
      while(count< 13){
        
        let list = []
        for (let i in this.fieldsCards){
          if(this.fieldsCards[i].x == count){
            list.push(this.fieldsCards[i])
          }
        }
        baseList.push(list)

        count++
      }

      return baseList
      
    },

    sideList: function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'side'){
          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.sideCount) - parseFloat(b.sideCount);
      });
      return list
    },

    heartList:function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'finished' && this.deckDetail[i].kind == 'heart'){

          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.num) - parseFloat(b.num);
      });
      return list
    },
    clubList:function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'finished' && this.deckDetail[i].kind == 'club'){

          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.num) - parseFloat(b.num);
      });
      return list
    },
    spadeList:function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'finished' && this.deckDetail[i].kind == 'spade'){

          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.num) - parseFloat(b.num);
      });
      return list
    },
    diamondList:function(){
      let list = []
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'finished' && this.deckDetail[i].kind == 'diamond'){

          list.push(this.deckDetail[i])
        }
      }
      list = list.sort(function(a, b) {
        return parseFloat(a.num) - parseFloat(b.num);
      });
      return list
    },

    deckNum: function(){
      let count =0
      for (let i in this.deckDetail){
        if(this.deckDetail[i].location == 'deck'){
          count++
        }
      }
      return count
    }
  },

  mounted: function(){
    this.isGameOver=  false,
    this.deckDetail =  undefined
    this.hasGameStarted= false
    this.sideCount=0
    this.isMultiple=false
    this.isMixedOver= false

    this.shuffledIndex=[]

    this.moveCount= 0
    this.showingTimer= '00:00'
    this.second= 0
    this.minute= 0
    this.hours=0 

    // this.startCounting()

    let count =0
    let kind = 'club'
    let num = 1
    let color = 'black'
    this.deckDetail =[]
    while(count<52){

      this.deckDetail.push({kind: kind,num: num,location: 'deck',x: 12, y: undefined, isOpened: false,color:color,cardId: `${kind}-${num}`, selected:false, movingNow: false})

      if(num ==13){
        num = 0
      }

      if(count ==12){
        
        kind = 'spade'
      }else if(count == 25){
        color = 'red'
        kind = 'heart'
      }else if(count == 38 ){
        kind = 'diamond'
      }

      count++
      num++
    
    }

    // console.log(this.deckDetail)
  },

  watch:{

    rows: {
      
      deep:true,
      handler() {
        for(let i in this.deckDetail){
          if(this.deckDetail[i].location == 'field'){
            
            if(this.deckDetail[i].y < 0  ){
              console.log(`${this.deckDetail[i].cardId}:  ${this.deckDetail[i].x}, ${this.deckDetail[i].y}`)
            }
          }
        }
        if(!this.hasGameStarted) return
        let count = 0
        let flag = true
        let falseCount= 0
        if(falseCount == 1) return
        while(count < 7){
          for(let i in this.rows[count]){
            if(!this.rows[count][i].isOpened){
              flag= false
              falseCount++
              
            }
            
          }

          
          count++
        }

        if(!flag){
          // console.log(`still going on with: ${falseCount} `)
          return 
        }
        if(!this.isGameOver){
          alert('You Won!');

        }
        console.log(' game is over')
        this.isGameOver = true
        this.spectate = true
        

      }
    },

    // deckDetail: {
      
    //   deep:true,
    //   handler() {
    //     for(let i in this.deckDetail){
    //       if(this.deckDetail[i].location == 'field' && this.deckDetail[i].isOpened ){
    //         let card = this.deckDetail[i]
    //         console.log(`Card: ${card.cardId}}, x: `)
    //       }
    //     }
    //     if(!this.hasGameStarted) return
    //     let count = 0
    //     let flag = true
    //     let falseCount= 0
    //     if(falseCount == 1) return
    //     while(count < 7){
    //       for(let i in this.rows[count]){
    //         if(!this.rows[count][i].isOpened){
    //           flag= false
    //           falseCount++
              
    //         }
            
    //       }

          
    //       count++
    //     }

    //     if(!flag){
    //       // console.log(`still going on with: ${falseCount} `)
    //       return 
    //     }
    //     if(!this.isGameOver){
    //       alert('You Won!');

    //     }
    //     console.log(' game is over')
    //     this.isGameOver = true
    //     this.spectate = true
        

    //   }
    // },
  },
}
</script>

<style>

body {
  touch-action: manipulation;
  overflow: hidden;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
  background-color: #34A249;
  width: 350px;
   /* width: 50%; */
  margin: 0 auto;


  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  -o-user-select: none;
  user-select: none;

}


.detail{
  /* background-color: grey; */
  height:30px;
  margin-top: 12px;
  text-align: start;
  margin-left: -15px;
}


.button {
  position: absolute;
  margin-left: 312px;
  margin-top: -3px;

  background-color:  darkgrey;
  border-width: 1px 1px 1px 1px;
  border-color: black;
  border-style: solid;
  color: black;

  padding: 3px 5px;

  text-align: center;
  text-decoration: none;
  /* display: inline-block; */
  font-size: 13px;

  cursor: pointer;
}


.finished-area{
  position:absolute;
  /* background-color: blue; */
  top:45px;
  /* opacity: 0.4; */
  width:200px;
  height:80px;
  display: flex;
  z-index: 200;
}


.top-section{
  margin-top: 3.5px;
  z-index: 1;
  /* background-color: blue; */
  height:80px;
  display: flex;
}

.bottom-section{
  z-index: 0;
  top:135px;
  /* width: 100%; */
  /* background-color: blue; */
  /* opacity: 0.2; */
  /* height:450px; */
  position: absolute;
}


.child{
  /* margin-top: 10px; */
  
  width: 42.7px;
  height: 50px;
  margin-right:6px;
  top: 0;
  /* right:0; */
}

.empty{
  border-width: 1px 1px 1px 1px;
  border-color: black;
  border-style: solid;
}

.back{
  background-color: crimson;
  border-width: 3px 3px 3px 3px;
  border-color: FloralWhite;
  border-style: solid;
  margin-top:-2px;
  height:50px;
  top:0;
  padding-top: 3px;
}

.start{
  background-color: crimson;
  border-width: 3px 3px 3px 3px;
  border-color: FloralWhite;
  border-style: solid;
  margin-top:-2px;
  height:53px;
  top:0;

  

  line-height: 54px;
  text-align: center;
}

.start span{
  display: inline-block;
  vertical-align: middle;
  line-height: normal;
  color:floralwhite;
  font-size: 70%;

}

.front{
  
  border-width: 3px 3px 3px 3px;
  border-radius: 5px;
  
  
  border-style: solid;
  margin-top:-2px;
  height:52px;
  top:0;
  font-size: 95%;
  padding-top: 2px;
}
.card-selected{
  box-shadow: 0px 0px 20px gray;
  transition: 0.3s;
  /* animate: glow; */
  background-color: #f0e130;
  border-color: #f0e130;
}

.card-not-selected{
  
  background-color: floralwhite;
  border-color: dimgrey;
}

.smallImage{
  position: relative;
  /* left:10px; */
  margin-top: -13px;
  /* padding-top: 4px; */
  margin-left: -1px;
  width:14px;
  height:auto;
}

.bigImage{
  position: relative;
  /* left:10px; */
  /* margin-top: 10px; */
  /* padding-top: 4px; */
  margin-left: 0px;
  width:26.5px;
  height:auto;
}

.front span{
  font-size: 95%;
  margin-right: 4px;
  margin-top: -10px;
}



</style>
