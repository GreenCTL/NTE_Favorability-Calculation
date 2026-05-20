<template>
    <!-- 左側欄 -->
    <div v-if="character" class="panel">
        
        <h1>
            {{ character.label }}
            <helpButton
             :image="character.list"/>
        </h1>
        
        <div class="content-area">
            <!-- 左側圖片 -->
            <div class="left-area">
                <img :src="character.fullBodyPhoto || 'https://i.meee.com.tw/ZJU93hQ.jpg'" class="full-body">
                <ul>
                    <li >
                        <!-- :style="{ backgroundImage: `url(${character.smallBodyPhoto || character.errorPhoto})` }" -->
                        送禮推薦:<br>{{ character.intro || character.error }}</li>
                </ul>
            </div>
            <!-- 右側好感計算器 -->
            <div class="right-area">
                <LoveCompute
                  :character="character"
                />
            </div>
        </div>
    </div>
</template>

<script setup>
import LoveCompute from './LoveCompute.vue';
import helpButton from './helpButton.vue';
defineProps({
    character: Object
})
</script>

<style scoped>
.panel {
    /* margin-top: 20px;
    padding: 20px; */
    border-radius: 10px;
    background: rgb(54, 54, 54);

    text-align: left;
}

.avatar {
    width: 30%;
    border-radius: 12px;
}

.full-body {
    width: 100%;
    height: 80%px;
    margin-top: 10px;
    object-fit: contain;
    display: block;
    border: 2px rgb(87, 77, 82) solid;
    border-radius: 50px;
    background-color: rgb(39, 39, 39);
}

h1 {
    color: rgb(231, 231, 231);
    height: 60%;
    width: 35%;
    text-align: center;
    transform: translate(10%,80%);
}

ul {
    list-style: none;
    padding: 0;
    margin: 0;
}

li {
    display: flex;
    align-items: center;
    gap: 5%;
    padding: 3% 0;
    color: rgb(228, 225, 225);
    margin-top: 2%;
    padding-left: 25%;
    /* 留空間給左邊的背景圖 (80px + 10px 間距) */
    /* min-height: 80px; */
    background-position: left center;
    background-size: contain;
    background-repeat: no-repeat;
}
.content-area{
    display: flex;
    gap: 3%;
    align-items: flex-start;
}
.left-area {
    width: 40%;
}
.right-area {
    flex: 1;
}
</style>