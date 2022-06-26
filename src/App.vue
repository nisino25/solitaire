<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <html>
    <head>

    </head>
    <body>
      <div class="wrapper">

        <div class="detail">

        </div>

        <div class="top-section">
          <div class="child empty"></div>
          <div class="child empty"></div>
          <div class="child empty"></div>
          <div class="child empty" style="margin-right: 33px"></div>
          <div class="child empty" style="margin-right: 30px"></div>
          <div class="child " style="margin-right: -6px" @click="startGame()">
            <div class="back"></div>
          </div>

        </div>

        <div class="bottom-section ">
          <!-- <div class="child empty">

          </div> -->

          <div class="child" :class="rows[0].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[0]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="rows[1].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[1]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="rows[2].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[2]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>
            
          <div class="child" :class="rows[3].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[3]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="rows[4].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[4]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" :class="rows[5].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[5]" :key="index">
              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>
            </template>
          </div>

          <div class="child" style="margin-right: -6px" :class="rows[6].length==0? 'empty' : ''">
            <template v-for="(card,index) in rows[6]" :key="index">

              <div v-if="!card.isOpened" class="back" :style="index==0? '':'margin-top:-40px'">
              </div>
              <div v-else class="front" :style="index==0? '':'margin-top:-40px'" >
                <span :style="{'color':card.color}">{{card.num}}</span>
                <img class="smallImage" :src="getSVG(card.kind)" alt="">
                <br>
                <img class="bigImage" :src="getSVG(card.kind)" alt="">
              </div>

            </template> 
          </div>

        </div>

        <!-- <div @click="test()">button kamo</div> -->

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
      deckDetail: undefined,
      hasGameStarted: false,
    }
  },

  methods:{
    startGame(){
      if(this.hasGameStarted) return
      this.hasGameStarted = true
      console.log('starting game')
      let count = 0
      let horizontal =1
      let vertical = 0

      while(count<21){
        let randomNum =Math.floor(Math.random() * 51);
        if(this.deckDetail[randomNum].location == 'deck'){
          this.deckDetail[randomNum].location = 'field'
          this.deckDetail[randomNum].x = horizontal
          this.deckDetail[randomNum].y = vertical
          vertical++

          if(horizontal== 0){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 1 && vertical ==1){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 2 && vertical ==2){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 3 && vertical ==3){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 4 && vertical ==4){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 5  && vertical ==5){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }else if(horizontal == 6  && vertical ==6){
            this.deckDetail[randomNum].isOpened = true
            horizontal++
            vertical =0
          }


          
          count++
        }
      }

      this.test()

    },

    test(){
      console.log(this.rows)
      // console.log('je')
      // for(let i in this.rows){

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==1) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==2) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==3) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==4) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==5) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }

      // for(let i in this.fieldsCards){
      //   if(this.fieldsCards[i].x ==6) {

      //     console.log(`${this.fieldsCards[i].kind}: ${this.fieldsCards[i].num}: x:${this.fieldsCards[i].x}, y:${this.fieldsCards[i].y}  `)
      //   }
      // }
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
      });
      return list
    },

    rows: function(){
      let baseList = []



      let count = 0
      while(count< 7){
        
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
      
    }
  },

  mounted: function(){
    let count =0
    let kind = 'club'
    let num = 1
    let color = 'black'
    this.deckDetail =[]
    while(count<51){

      this.deckDetail.push({kind: kind,num: num,location: 'deck',x: undefined, y: undefined, isOpened: false,color:color})

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
}
</script>

<style>
body {
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

}


.detail{
  background-color: grey;
  height:100px;
  margin-top: 50px;
}

.top-section{
  /* background-color: blue; */
  height:120px;
  display: flex;
}

.bottom-section{
  /* background-color: yellow; */
  height:420px;
  display: flex;
}

.child{
  margin-top: 10px;
  
  width: 42.7px;
  height: 60px;
  margin-right:6px;
  /* box-shadow: 0 0 1px 1px black; */
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
  height:59px;
  top:0;
  padding-top: 3px;
}

.front{
  background-color: floralwhite;
  border-width: 3px 3px 3px 3px;
  border-radius: 5px;
  border-color: dimgrey;
  border-style: solid;
  margin-top:-2px;
  height:59px;
  top:0;
  font-size: 95%;
  padding-top: 3px;
}

.smallImage{
  position: relative;
  /* left:10px; */
  /* margin-top: 10px; */
  /* padding-top: 4px; */
  /* margin-left: 0px; */
  width:15px;
  height:auto;
}

.bigImage{
  position: relative;
  /* left:10px; */
  /* margin-top: 10px; */
  /* padding-top: 4px; */
  margin-left: 3px;
  width:30px;
  height:auto;
}

.front span{
  margin-right: 4px;
  /* margin-top: -20px; */
}

</style>
