<script setup lang="ts">
import { ref,onMounted, onUpdated } from 'vue';
import { VueDraggableNext } from 'vue-draggable-next'
import { MaskInput } from "maska"
let id = 0;
let delitingValue = 0;
interface Item {
  id: number;
  count: number;
  isItem: boolean;
  color?: string;
}

let items = ref<Array<Item>>([
  {id: id++, isItem: true, color: "green", count: 4},
  {id: id++, isItem: true, color: "yellow", count: 2},
  {id: id++, isItem: true, color: "purple", count: 5},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0},
  {id: id++, isItem: false, count: 0}
])

const isModalActive = ref<boolean>(false);
const isFramelActive = ref<boolean>(true);
const color = ref<string>("green");
const modalId = ref<number>(0)
const isDeliting = ref<boolean>(false);

const hideModal = ():void => {isModalActive.value = false;}
const hideFrame = ():void => {isFramelActive.value = false;}
const delitingStart = ():void => {isDeliting.value = true;}
const cancelDeliting = ():void => {isDeliting.value = false;}

const openModal = (item_id: number):void => {
  modalId.value = item_id;
  items.value.forEach(item => {
    if(item.id == modalId.value && item.isItem == true){
      color.value = item.color;
    }
  })
  isModalActive.value = true;
}

const confirmDeliting = ():void => {
  items.value.forEach(item => {
    if(item.id == modalId.value && item.isItem == true && item.count >= delitingValue){
      item.count = item.count - delitingValue;
      cancelDeliting();
      hideModal();
      if(item.count == 0) {
        item.isItem = false;
      }
    }
  })
}

const save = ():void => {
  localStorage.setItem("itemsArray", JSON.stringify(items.value))
}

onMounted(() => {
  if (localStorage.getItem("itemsArray")){
    items.value = JSON.parse(localStorage.getItem("itemsArray"))
    }
})

onUpdated(()=>{
    localStorage.setItem("itemsArray", JSON.stringify(items.value))
  })
</script>


<template>
  <div class="flex-center">
    <div class="flex-container">
      <div class="info-block">
        <img class="info-image" src="./assets/ImgBlur.png" alt="ImgBlur">
        <span class="blured-title"></span>
        <span class="blured-text_1"></span>
        <span class="blured-text_2"></span>
        <span class="blured-text_3"></span>
        <span class="blured-text_4"></span>
        <span class="blured-text_5"></span>
        <span class="blured-text_6"></span>
      </div>
      <div class="inventory">
        <div :class="isModalActive ? 0 : 'disabled'" class="inventory-modal">
          <span class="hide-button" @click="hideModal()"><img src="./assets/hide.png"></span>
          <span :class='`inventory-modal_item-${color}`'></span>
          <hr class="border">
          <div class="text-block">
            <span class="blured-title"></span>
            <span class="blured-text_1"></span>
            <span class="blured-text_2"></span>
            <span class="blured-text_3"></span>
            <span class="blured-text_4"></span>
            <span class="blured-text_5"></span>
            <hr class="border">
            <span class="delete-button" @click="delitingStart()">Удалить предмет</span>
            <div :class="isDeliting ? 0 : 'hiden'" class="delete-item_block">
              <input type="number" v-maska data-maska="#" v-model="delitingValue" placeholder="Введите количество" class="masked delete-item_input">
              <div class="delete-item_buttons">
                <span class="cancel-button" @click="cancelDeliting()">Отмена</span>
                <span class="confirm-button" @click="confirmDeliting()">Подтвердить</span>
              </div>
            </div>
          </div>
        </div>
          <VueDraggableNext class="inventory-greed" @change="save()"  :list="items" handle=".handle" :sort="true">
            <div v-for="item in items" class="inventory-block" :class="item.isItem ? 'handle' : 0" :key="item.id">
              <span v-if="item.isItem" :class='`inventory-item-${item.color}`' @click="openModal(item.id)"></span>
              <span v-if="item.isItem" class='inventory-item-count'>{{item.count}}</span>
            </div>
          </VueDraggableNext>
      </div>
    </div>
  </div>

  <div v-if="isFramelActive" class="flex-center">
    <div class="bottom_frame">
      <span class="hide-button" @click="hideFrame()"><img src="./assets/hide.png"></span>
      <div class="blured-text"></div>
    </div>
  </div>

</template>

<style lang="scss">
@import url('https://fonts.cdnfonts.com/css/sf-pro-display');
$primary-color: #4D4D4D;
$primary-background: #dedede;

@mixin blured-text($width, $min-width){
  background: linear-gradient(90deg, #3C3C3C 0%, #444444 51.04%, #333333 100%);
  width: $width;
  min-width: $min-width;
  border-radius: 8px;
  height: 3vh;
  min-height: 12px;
  margin-top: 4vh;
  display: block;
}

@mixin block{
  background: $primary-background;
  border: 1px solid $primary-color;
  border-radius: 12px;
  overflow: hidden;
}

@mixin rectangle($translucent-color, $normal-color, $min-size,  $size){
  position: relative;
  background: $normal-color;
  min-width: $min-size;
  min-height: $min-size;
  width: $size;
  height: $size;
  transform: translate(-10%,10%);
  &:before{
    content: "";
    display: block;
    backdrop-filter: blur(6px);
    filter: blur(6px);
    background: $translucent-color;
    min-width: $min-size;
    min-height: $min-size;
    width: $size;
    height: $size;
    transform: translate(10%,-10%);
  }
}

body {
  background-color: #fff; 
  font-family: 'SF Pro Display', sans-serif;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

.flex{
 &-container{
    display: flex;
    justify-content: space-between;
    margin-top: 32px;
    width: 95%;
  }
  &-center{
    display: flex;
    justify-content: center;
  }
}
.info{
  &-image{
    margin-top: 18px;
  }
  &-block{
    @include block();
    width: 25%;
    padding: 0 14px 0 14px;
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 125vh;
    img{
      width: 100%;
    }
  }
}

.blured{
  &-title{
    @include blured-text(80%,190px);
    height: 6vh;
  }
  &-text{
    &_1{
      @include blured-text(65%,155px);
      margin-top: 6vh;
    }
    &_2{@include blured-text(80%,190px);}
    &_3{@include blured-text(72%,170px);}
    &_4{@include blured-text(67%,160px);}
    &_5{ @include blured-text(59% ,140px);}
    &_6{
      @include blured-text(24%,80px);
      margin-top: 6vh 0 6vh 0;
    }
  }
}

.inventory{
  @include block();
  position: relative;
  width: 60vw;
  height: 125vh;
  overflow: hidden;
  &-block{
    @include block();
    border-radius: 0;
    position: relative;
    border: 1px solid $primary-color;
  }
  &-greed{
    display: grid;
    grid-template-columns: repeat(5,1fr);
    grid-auto-rows: 25vh;
  }
  .disabled{
    right: -50%;
    border: none;
  }
  .delete-item{
    &_block{
      width: 100%;
      height: 30vh;
      bottom: 0;
      display: flex;
      flex-direction: column;
      justify-content: space-around;
      align-items: center;
      position: absolute;
      background: $primary-background;
      border-top: 1px solid $primary-color;
      transition: .5s;
    }
    &_input{
      border: 1px solid $primary-color;
      border-radius: 4px;
      background: $primary-background;
      width: 84%;
      font-size: 18px;
      padding: 1em;
      box-sizing: border-box;
      outline: none;
    }
    &_buttons{
      width: 84%;
      display: flex;
      justify-content: space-between;
      .cancel-button{
        background: #FFFFFF;
        border-radius: 8px;
        text-align: center;
        padding: 1em 2em 1em 2em;
        font-size: 18px;
        cursor: pointer;
      }
      .confirm-button{
        background: #FA7272;
        color: #fff;
        border-radius: 8px;
        text-align: center;
        padding: 1em 2em 1em 2em;
        font-size: 18px;
        cursor: pointer;
      }
    }
  }
  
  .hiden{
    bottom: -400px;
    border: none;
    position: absolute;
  }

  &-item{
    &-green{
      @include rectangle(rgba(184, 217, 152, 0.35),#7FAA65, 48px, 6vw);
      margin: 3vh 0vw 0vh 3.5vw;
      display: block; 
      cursor: pointer;
    }
    &-yellow{
      @include rectangle(rgba(217, 187, 152, 0.35),#AA9765, 48px, 6vw);
      margin: 3vh 0vw 0vh 3.5vw;
      display: block; 
      cursor: pointer;
    }
    &-purple{
      @include rectangle(rgba(116, 129, 237, 0.35),#656CAA, 48px, 6vw);
      margin: 3vh 0vw 0vh 3.5vw;
      display: block; 
      cursor: pointer;
    }
    &-count{
      color: #fff;
      position: absolute;
      bottom: 0;
      right: 0;
      border: 1px solid $primary-color;
      border-radius: 6px 0px 0px 0px;
      padding: .1em .5em .1em .5em;
    }
  }
  &-modal{
    position: absolute;
    width: 50%;
    height: 100%;
    overflow: hidden;
    border-left: 1px solid #4D4D4D;
    border-radius: 0 12px 12px 0;
    backdrop-filter: blur(8px);
    transition: .5s;
    right: 0;
    top: 0;
    z-index: 10;
  }
  .hide-button{
    display: flex;
    justify-content: end;
    cursor: pointer;
    img{
      margin: 14px;
      width: 2vh;
      min-width: 12px;
    }
  }
  &-modal_item{
    &-green{
      @include rectangle(rgba(184, 217, 152, 0.35),#7FAA65, 115.56px, 20vw);
      margin: 5vh 5vw 5vh 6vw;
      display: block; 
    }
    &-yellow{
      @include rectangle(rgba(217, 187, 152, 0.35),#AA9765, 115.56px, 20vw);
      margin: 5vh 5vw 5vh 6vw;
      display: block; 
    }
    &-purple{
      @include rectangle(rgba(116, 129, 237, 0.35),#656CAA, 115.56px, 20vw);
      margin: 5vh 5vw 5vh 6vw;
      display: block; 
    }
  }
  .border{
      display: block;
      width: 90%;
      margin-top: 6vh;
      border-bottom: 1px solid $primary-color;
    }
  .text-block{
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .delete-button{
    text-align: center;
    background: #FA7272;
    border-radius: 8px;
    width: 90%;
    color: #fff;
    min-height: 39px;
    margin-top: 2vh;
    padding-top: 1em;
    cursor: pointer;
  }
  .blured{
    &-title{
      @include blured-text(84%,211px);
      height: 6vh;
    }
    &-text{
      &_1{
        @include blured-text(84%,211px);
        margin-top: 6vh;
      }
      &_2{@include blured-text(84%,211px);}
      &_3{@include blured-text(84%,211px);}
      &_4{@include blured-text(72%,180px);}
      &_5{
        @include blured-text(32%,80px);
        margin-top: 6vh 0 6vh 0;
      }
    }
  }
}

.bottom_frame {
  @include block();
  margin: 6vh 0 8vh 0;
  width: 95%;
  height: 18vh;
  .blured-text{
    @include blured-text(90%,689px);
    height: 9vh;
    min-height: 36px;
    margin-top: 0;
    margin-left: 3vw;
  }
  .hide-button{
    display: flex;
    justify-content: end;
    cursor: pointer;
    img{
      margin: 14px;
      width: 2vh;
      min-width: 12px;
    }
  }
}

@media (prefers-color-scheme: dark) {
  $primary-background: #262626;
  .info-block,.bottom_frame, .inventory, .inventory-block, .inventory .delete-item_block, .inventory .delete-item_input {
    background: $primary-background;
  }

  .inventory .delete-item_input{
    color: #fff;
  }

  .inventory-modalХ{
    background: rgba(38, 38, 38, 0.5);
  }

  body{
    background-color: #1E1E1E;
  }
}
</style>
