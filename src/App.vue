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
            <!-- <div v-if="!hasGameStarted" class="button" @click="startGame()" style="background-color:  white;">START</div> -->
            <div v-if="hasGameStarted" class="button" @click="reset()" style="background-color:  darkgrey;">RESET</div>
            
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

        <div class="finished-area" v-if="hasSelectedCard" @click="finishDrop()"></div>

        <div class="top-section">

          <div><div class="empty child"></div></div>
          <div><div class="empty child"></div></div>
          <div><div class="empty child"></div></div>
          <div><div class="empty child"></div></div>

          <!-- ---- pile --------------->
          <div style="margin-left: 25px;">
            <div style="position: absolute; width: 43px; z-index: 0; " >
              <div  class="empty child" ></div>
            </div>
          </div>

          <!-- ---- deck --------------->


          <div class="child holder " :class="deckNum == 0? 'empty' : ''" style="margin-left: 26px;  z-index: 1000" @click="startGame()" >

            <div v-if="deckNum !==0 && hasGameStarted" class='back'></div>
            <div v-if="deckNum ==0 && hasGameStarted"> <img style="margin-top:13px;" src="../public/iconmonstr-refresh-1.svg" ></div>
            
            <div v-if="!hasGameStarted" class="start"><span>START</span> </div>
            
          </div>
          <span style="margin-top: 62px; color:FloralWhite; font-weight:bold; text-align: middle;" :style="[deckNum >= 10 ? 'margin-left:-39px ' : 'margin-left:-35px']">{{deckNum}}</span>

        </div>

        <div class="bottom-section" >


          <!-- this for empty space to put it back -->
          <template v-for="(row,rowIndex) in rows" :key="rowIndex" >

            <div v-if="rowIndex < 7 "  :style="{'left': rowIndex * 51  + 'px'}" style="position: absolute; width: 46px; z-index: 0; top:20px " >
              <div  class="empty child" @click="toEmptySpace(rowIndex)"></div>
            </div>

          </template> 

          <div>
            <template  v-for="(card) in deckDetail" :key="card" >


              <div style="position: absolute; width: 43px; transition : all 0.6s ease 0s;" :style="getStyle(card)" v-if="card" >



                  
                  <!-- ----------- back face card  ------- -->
                  <div v-if="!card.isOpened && card.location == 'field'" class="back" @click="cardClick(card,'pick')"   >
                  </div>



                  <!-- ----------- front face card  ------- -->
                  <div v-if=" card.isOpened  " class="front"  @click="cardClick(card,'pick')"  :class="[card.selected ? 'card-selected' : 'card-not-selected']" >
                    <span :style="{'color':card.color}">{{convertNum(card.num)}}</span>
                    <!-- <span>heys</span> -->
                    <img class="smallImage" :src="getSVG(card.kind)" alt="">
                    <br>
                    <img  class="bigImage" :src="getSVG(card.kind)" alt="">
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
// import { time } from "console"

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
      deal_auido: new Audio(require('@/assets/sounds/deal.wav')),
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
    sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    },
    async startGame(){
      

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
          await this.sleep(25);
          this.deckDetail[randomNum].location = 'field'
          this.deckDetail[randomNum].x = horizontal
          this.deckDetail[randomNum].y = vertical
          vertical++
          // this.deckDetail[randomNum].isOpened = false

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
            this.shuffle_audio.play();
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
          }else{
            this.deckDetail[randomNum].isOpened = false
          }


          
          count++
        }
      }
      // var shuffle_audio = new shuffle_audio('../public/shuffle_audio/shuffle_sound.wav');
      this.shuffle_audio.play();
      // console.log(this.deckDetail)


      this.test()

    },

    openMoreCard(){
      this.allUnselected()
      // if it is A or the ones following then put it into the corner

      // reset cards to decks
      if(this.deckNum ==0){
        this.stopAllTheSound()
        this.shuffle_audio.play()
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
        console.log('he')
        // this.deal_auido.pause()
        this.deal_auido.play()
        for(let i in this.shuffledIndex){
          let theIndex = this.shuffledIndex[i]
          if(this.deckDetail[theIndex].location == 'deck'){
            this.deckDetail[theIndex].location = 'side'
            this.deckDetail[theIndex].isOpened = true
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
          // this.dekDetail[randomNum].x = 4.5
          this.deckDetail[randomNum].isOpened = true
          this.sideCount++
          flag =true
          this.moveCount++
          // console.log(this.deckDetail[randomNum].cardId)
        }
      }
      // let myAudio = document.getElementById('myAudio');
      // myAudio.play();
      
      this.deal_auido.play()
      
    },

    test(){
    },

    reset(){
    if(!this.hasGameStarted) return this.startGame() 
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

    isFinalCard(index,card){
      // console.log(card.cardId)
      if(card.location == 'side'){
        let lastIndex = this.sideList.length
        
        if(index+1 == lastIndex){
          return true
        }else{
          return false
        }
      }

      // --------------------------------------------
      // console.slog(index)

      // what if it is multile 
      
      let lastIndex = this.rows[card.x].length
      if(card.y+1 == lastIndex){
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
      if(card.location == 'deck' && !this.hasGameStarted) return this.startGame()
      if(card.location == 'deck' && this.hasGameStarted) return this.openMoreCard()

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
          // console.log('this?')


          // right here ------------------------------------------------------------
          // changing x location
          previous.x = current.x
          previous.y = current.y + 1
          // console.log(`previous: ${card.cardId}, x: ${previous.x}, y: ${previous.y}`)
        }

        
        // console.log('this?')
        this.hasSelectedCard = false
        previous.movingNow = true

        // this.deal_auido.pause()
        this.deal_auido.play()
        
        this.allUnselected()
        // setTimeout(() => this.basketAddSuccess = false, 2000);
        setTimeout( () => {
          this.stopEverything()
        }, 500);


        previous.location = 'field'

        // console.log(`previous: ${card.cardId}, x: ${previous.x}, y: ${previous.y}`)
        // console.log(`location : ${previous.location}`)
        

        
    
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
      if(card.location == 'side') return 1000
      if(!card.movingNow ) return  card.y

      return 100 - card.y
    },

    getHeight(index){
      if(this.sideList.length ==1){
        return -91 + 'px'
      }


      if(this.sideList.length ==2){
        if(index == 0){
          return -91 + 'px'
        }else if(index == 1){
          return 69 -136 + 'px'
        }
      }


      // let position =  this.sideList.length  - index
      // console.log(position)
      // position = position/ index
      if(index == 0){
        return -91 + 'px'
      }else if(index == 1){
        return -67 + 'px'
      }else if(index == 2){
        return -45 + 'px'
      }
      


      // return 100+  position * 30+ 'px'
    },

    stopAllTheSound(){
      // this.shuffle_audio.pause()
      // this.move_audio.pause()
      // this.deal_auido.pause()
    },

    getStyle(card){
      if(card.location == 'field'){
        return`left: ${card.x * 51}px; top: ${card.y * 23+ 20}px; z-index: ${this.getZ(card)}` 

      }else if (card.location == 'side'){
        if(this.sideCheck(card) == 'no') return `opacity:0;right: -270px; top:-80px;`

        let index = this.sideCheck(card)
        return `top: ${this.getHeight(index)}; left: 227px; z-index: ${100 +index} 
        `
      }else if(card.location == 'deck'){
        return `top: -95px; left:300px; opacity: 0; z-index: 0;  `

      }else if(card.location == 'finished'){
        return this.getCordninate(card)
      }
      // {'top': card.y * 23  + 'px', 'left': card.x * 51  + 'px','z-index' : getZ(card)}
      // return `top: ${card.y * 23}  + px}; left: ${card.x * 51}  + px; z-index: ${this.getZ(card)}`
    },

    sideCheck(card){
      if(this.sideList.length == 1){
        if(this.sideList[0].cardId == card.cardId){
          
          return 0
        }else {
          // console.log(`og: ${card.cardId}, answe: ${this.sideList[0].cardId}`)
          return 'no'
        }
        
      }else if(this.sideList.length == 2){
        if(this.sideList[0].cardId == card.cardId) {
          return 0
        }else if(this.sideList[1].cardId == card.cardId) {
          return 1
        }return 'no'

      }else if(this.sideList.length > 2  ){
        // console.log('here?s')

        let theIndex = this.sideList.length -1

        let first = this.sideList[theIndex-2]
        let second = this.sideList[theIndex-1]
        let third = this.sideList[theIndex]
        // console.log(first.cardId)
        // console.log(second.cardId)
        // console.log(third.cardId)
        if(first.cardId == card.cardId){
          
          return 0
        } else if(second.cardId == card.cardId){
          return 1
        } else if(third.cardId == card.cardId){
          return 2
        }else{
          return 'no'
        }
        
        
      }
    },

    getCordninate(card){
      let leftPx

      switch(card.kind){
        case 'heart':
          leftPx = '0px'
          break;

        case 'club':
            leftPx = '50px'
            break;

        case 'diamond':
          leftPx = '100px'
          break;

        case 'spade':
          leftPx = '150px'
          break;
      }
      let style = `top: -90px; left:${leftPx}; z-index: ${card.num}`
      // console.log(style)
      return style;
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
    },

    stylesForEach: function(){
      let list = []
      for(let i in this.deckDetail){
        let card =this.deckDetail[i]
        if(card.location == 'field'){
          // console.log(`field; ${card.cardId}`)
          list.push(`left: ${card.x * 51}px; top: ${card.y * 23}px; z-index: ${this.getZ(card)}`)       
        }else if (card.location == 'side'){
          // list.push(`top: -136px; left: 100px; `) 
          // return
          console.log(`side; ${card.cardId}`)
          if(this.sideCheck(card) == 'no'){
            list.push(`top: -200px; left: 100px; `)

          }

          let index = this.sideCheck(card)


        list.push(`top: ${this.getHeight(index)}; right: -272px; z-index: ${index+ 100} `)
        }else if(card.location == 'deck'){
          list.push(`top: -130px; left: 200px; `) 
        }
      }
      return list
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

      this.deckDetail.push({kind: kind,num: num,location: 'deck',x: 5.5, y: undefined, isOpened: true,color:color,cardId: `${kind}-${num}`, selected:false, movingNow: false,deckIndex: count})

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
          if(this.moveCount>10)
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
.wrapper{
  width: 375px;
  margin: 0 auto;
  /* background-color: #f0e130; */

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
  margin-left: 316px;
  margin-top: -3px;

  
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
  height:100px;
  z-index: 200;
}


.top-section{
  margin-top: 3.5px;
  z-index: 0;
  /* background-color: blue; */
  height:80px;
  position: absolute;
  display: flex;
}

.top-section div{
  width: 38px;
  z-index: 0;
  margin-right:13px;
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


.holder{
  /* margin-top: 10px; */
  
  width: 42.7px;
  height: 50px;
  margin-right:6px;
  top: 0;
  /* right:0; */
}

.child{
  /* margin-top: 10px; */
  
  /* width: 42.7px; */
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



/* animation ------------- */
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}




</style>
