<template>
  <div id="app" class="nte-tools-page">
    <h2>【異環】角色好感度計算器</h2>
    <div id="selectArea">
      <DropdownMenu label="異能者選擇" :items="toolOptions" v-model="currentTool" />
      <CharacterPanel v-if="currentTool" :character="currentTool" />
      <h2 v-else>請選擇一名角色</h2>
    </div>
  </div>
</template>

<script setup>
import DropdownMenu from './DropdownMenu.vue';
import { ref } from 'vue';

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
  /* background-color: rgb(61, 177, 177); */
  padding: 20px;
  border-radius: 8px;
  display: inline-block;
}

h2 {
  color: rgb(255, 255, 255);
  margin-top: 15px;
  font-size: 35px;
}
</style>