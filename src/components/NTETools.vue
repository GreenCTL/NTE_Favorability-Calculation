<template>
  <div id="app" class="nte-tools-page">
    <h2>【異環】角色好感度計算器</h2>
    
    <div id="selectArea">
      
      <DropdownMenu class="DropDownMenu" label="異能者選擇" :items="toolOptions" v-model="currentTool" />
      <a 
      href="https://1drv.ms/x/c/68dbb3e58497c8b8/IQCxHFJP3Y0wRpXlU_prkqPhAexfmCgZ38pSwUaxvAbfQFk?e=zr3qXo"
      target="_blank">好感度表格
      <div class="imgbox">
      <img class="sheetimg" src="https://i.meee.com.tw/GDuHH3M.png">
      <img class="sheetimg-hover" src="https://i.meee.com.tw/ab0sSWG.png">
      </div>
    </a>
      <CharacterPanel v-if="currentTool" :character="currentTool" />
      <h2 v-else>請選擇一名角色</h2>
    </div>
  </div>
  <footer>
    <p><VisitorCounter /></p>
  </footer>
</template>

<script setup>
import DropdownMenu from './DropdownMenu.vue';
import { ref } from 'vue';
import VisitorCounter from './VisitorCounter.vue'

import { characters } from './Characters.js';
import CharacterPanel from './CharacterPanel.vue'
const toolOptions = characters

const currentTool = ref(null)

</script>

<style scoped>
.nte-tools-page{
  background-color: rgb(29, 28, 28);
}
.current-status {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px;
  margin-top: 10px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 4px;
}

.avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  object-fit: cover;
}

#selectArea {
  /* border: 5px white solid; */
  padding: 20px;
  border-radius: 8px;
  display: inline-block;
  align-items: center;
}

h2 {
  color: rgb(255, 255, 255);
  margin-top: 15px;
  font-size: 36px;
}
.DropDownMenu{
  color: rgb(82, 30, 30);
  /* margin-top: 15px; */
  /* margin-left: 10%; */
  /* margin-right: 10%; */
  font-size: 100%;
}
a{
  /* border: 5px white solid; */
  color: rgb(255, 255, 255);
  position: absolute;
  left: 60%;
  top: 5%;
  transform: translateX(30%);
  font-size: 100%;
    display: flex;          
  align-items: center;    
  /* gap: 10px;               */
}
a:hover{
  color: rgb(156, 156, 156);
  border-radius: 30%;
}
a:hover .sheetimg-hover{
  opacity: 1;
}

/* hover時隱藏第一張 */
a:hover .sheetimg{
  opacity: 0;
}
.imgbox{
  position: relative;
  width: 110px;
  height: 110px;
}

/* 兩張圖片重疊 */
.imgbox img{
  position: absolute;

  top: 0;
  left: 0;

  width: 100%;
  height: 100%;

  object-fit: cover;

  transition: opacity .3s ease;
}

/* hover圖預設隱藏 */
.sheetimg-hover{
  opacity: 0;
}

/* hover時顯示第二張 */
.imgbox:hover .sheetimg-hover{
  opacity: 1;
}

/* hover時隱藏第一張 */
.imgbox:hover .sheetimg{
  opacity: 0;
}
</style>