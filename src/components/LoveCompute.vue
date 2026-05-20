<template>
    <!-- 好感度計算 -->
    <div class="info-box">
        <div class="inputArea">
            <h2 style="color: white;">好感度計算器</h2>
            <div class="inputArea1">
                請輸入目前好感度等級:
                <select v-model="currentLevel" style="width: 180px;">
                    <option :value="null" disabled>請選擇等級</option>
                    <option v-for="level in 10" :key="level-1" :value="level-1">
                        {{ level-1 }}
                    </option>
                </select>
            </div>

            <div class="inputArea2">
                請輸入目前好感度經驗:
                <input min="0" v-model.number="currentExp" type="number" placeholder="請輸入經驗值" @input="checkValue"
                    class="levelSelecter" @keydown="blockInvalidChar">
                </input>
            </div>
            <!-- 錯誤輸入提醒 -->
            <div class="error-wrapper">
                <div v-show="errorMessage" class="error-text">
                    ⚠️ {{ errorMessage }}
                </div>
            </div>


            <button style="color: white;" @click="handleCalculate">生成結果</button>
            <!-- 顯示區 -->
            <div v-if="remainingExp !== 0" class="result-display">
                <template v-if="remainingExp === -1">
                  好感度已滿！
                </template>

                <template v-else>
                 <p class="remaining-title">還差 {{ remainingExp }} 經驗值滿好感</p>
                 <div 
                 v-for="(plan,index) in calculatedPlan"
                 :key="index"
                 class="plan-card"
                 style="margin-bottom: 10px;"
                 >
                    <div class="gift-info">
                        <img :src="plan.src" alt="禮物圖片" class="gift-img">
                        <div class="gift-text">
                            
                            <div class="details">
                                <p>預計所需時間:<span class="highlight">{{ plan.days }}</span>天</p>
                                <p>還需要送
                                    <span class="gift-name">{{ plan.giftCount }}</span>
                                    個
                                    <span class="gift-name">{{ plan.name }}</span>
                                </p>
                                <p>預計總花費:<span class="price-text">{{ plan.totalPrice }}</span>方斯</p>
                            </div>
                        </div>
                    </div>
                 </div>
                </template>          
                </div>
        </div>

    </div>
</template>

<script setup>
import { ref, watch } from 'vue';
import DropdownMenu from './DropdownMenu.vue';
const props = defineProps({
    character: Object
})
//切換角色時重置輸入選項和錯誤訊息
watch(
    () => props.character,
    () => {
        currentExp.value = 0
        currentLevel.value = 0
        remainingExp.value = 0
        errorMessage.value = ""
    }
)
// 定義變數
const currentLevel = ref(null)
const currentExp = ref(0)
const remainingExp = ref(0)
//等級換算經驗值定義(累積等級)
const levelExpMap = {
 0: 0,
    1: 100,
    2: 600,
    3: 1600,
    4: 3600,
    5: 7100,
    6: 12100,
    7: 19100,
    8: 28100,
    9: 40100
};
// 定義計算方法
const calculatedPlan = ref([]);
// 定義錯誤訊息的變數
const errorMessage = ref("");
errorMessage.value = "";
//防止經驗值輸入負數
const checkValue = () => {
    if (currentExp.value < 0 || currentExp.value === null) {
        currentExp.value = 0
    }
    if (currentExp.value > 56100) {
        currentExp.value = 56100
    }
}
//攔截非法輸入
const blockInvalidChar = (e) => {
    // 允許的按鍵列表：數字 0-9、退格 (Backspace)、刪除 (Delete)、方向鍵
    const allowedKeys = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'Backspace', 'Delete', 'ArrowLeft', 'ArrowRight', 'Tab'];

    // 如果按下的鍵不在允許列表內，就阻止輸入 (例如 e, -, +, ., ,)
    if (!allowedKeys.includes(e.key)) {
        e.preventDefault();
    }
};
//檢查流程
const handleCalculate = () => {
    errorMessage.value = "";
    remainingExp.value = 0
    //檢查是否選擇等級
    if (currentLevel.value === null) {
        errorMessage.value = "請先選擇好感度等級!"
        remainingExp.value = 0 //清除之前的結果
        return;
    }
    //檢查是否輸入經驗值
    if (currentExp.value === null || currentExp.value === "") {
        errorMessage.value = "請輸入好感度等級!"
        remainingExp.value = 0
        return;
    }

    // if (currentLevel.value === 0) {
    //     alert("請先選擇等級!")
    //     return
    // }
    // if (currentExp.value < 0 || currentExp.value === null) {
    //     alert("請輸入經驗值")
    //     return
    // }
    //定義計算方法
    const baseExp = levelExpMap[currentLevel.value] || 0
    const totalMaxExp = 51600
    const result = totalMaxExp - currentExp.value - baseExp

    //測試用
    console.log("當前等級:", currentLevel.value)
    console.log("當前經驗", currentExp.value)
    //開始計匴    
    if (result <= 0) {
        remainingExp.value = -1;
    } else {
        remainingExp.value = result;
    }
    //抓取角色gift資料
    calculatedPlan.value = []; // 每次計算前清空
    const characterGifts = props.character?.gifts;
    if(characterGifts && characterGifts.length>0){
       calculatedPlan.value = characterGifts.map(gift => {
    //計算公式
        //每日經驗
        const dailyExp = gift.exp*3
        //每日花費
        const dailyCost = gift.price *3
        //總共需要多少禮物
        const giftCount = Math.ceil(remainingExp.value / gift.exp)
        //還需要多少天
        const days = Math.ceil(remainingExp.value/dailyExp)
        const totalCost = days*dailyCost
        return {
            src:gift.src,
            name:gift.giftName || props.character.intro,
            days:days,
            totalPrice:totalCost,
            giftCount: giftCount
        }
    })

    }
    //測試察看結果
    console.log(`等級 ${currentLevel.value} 換算經驗為: ${baseExp}`)
}

</script>

<style scoped>
.info-box {
    background: rgba(255, 255, 255, .1);
    padding: 5%;
    border-radius: 10%;
    min-height: 30%;
    height: auto;      /* 讓高度隨內容自動增長 */
    display: flex;
    flex-direction: column;
}

h2 {
    text-align: center;
}

button {
    width: 40%;
    background-color:#4a7446;
    border: rgb(255, 255, 255) 0.3px solid;
    border-radius: 30px;
    padding: 2%;
    margin-bottom: 3%;
    cursor: pointer;
}

button:hover {
    transform: translateY(-3px);
    /* 向上微移 */
    box-shadow: 0 10px 20px rgba(33, 148, 17, 0.2);
    /* 增加發光的青色陰影 */
}

button:active {
    transform: translateY(-1px);
    /* 稍微回彈 */
    box-shadow: 0 5px 10px rgba(0, 255, 255, 0.2);
}

.inputArea {
    /* height: 300px; */
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    gap: 20px;
}
.result-display{
 /* 與按鈕保持距離 */
    padding: 5%;
    background: rgba(26, 114, 196, 0.2); /* 淡淡的成功綠背景 */
    border-radius: 10px;
    color: #e6e6ee;    /* 亮綠色文字 */
    font-weight: bold;
    text-align: center;
    width: 100%;       /* 寬度撐滿 */
    box-sizing: border-box;
    animation: fadeIn 0.4s ease; /* 增加一個淡入效果 */
}
@keyframes fadeIn {
    from { opacity: 0; transform: translateY(-10px); }
    to { opacity: 1; transform: translateY(0); }
}
.inputArea1 {
    text-align: center;
    color: white;
}

.inputArea2 {
    text-align: center;
    color: white;
}

.result-text {
    color: #52c41a;
    /* 成功綠 */
    font-weight: bold;
    margin-top: 10px;
}

.error-wrapper {
    height: 25px;
    /* 固定高度，預留給錯誤訊息 */
    display: flex;
    align-items: center;
    justify-content: center;
}

.error-text {
    color: #ff4d4f;
    font-size: 0.9rem;
    font-weight: bold;
}
.remaining-title {
    margin-bottom: 3%;
    font-size: 1.1rem;
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);
    padding-bottom: 10px;
}

.plan-card {
    background: rgba(0, 0, 0, 0.3);
    border-radius: 12px;
    padding: 1%;
    text-align: left;
}

.gift-info {
    display: flex;
    align-items: center;
    gap: 15px;
}

.gift-img {
    width: 50%;
    
    border-radius: 20px;
    object-fit: cover;
    border: 2px solid #ffffff;
}

.gift-text {
    flex: 1;
}

.gift-name {
    font-size: 100%;
    color: #8fb6ff; /* 給予商品名稱一個柔和的藍色 */

}

.details p {
    margin:  0;
    font-size: 70%;
}

.highlight {
    color: #ffd700; /* 金色顯示天數 */
    font-weight: bold;
    font-size: 120%;
}

.price-text {
    color: #ff8c00; /* 橘色顯示金額 */
    font-weight: bold;
}
</style>