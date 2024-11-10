<template>
  <div class="background">
    <!-- 進度條 -->
    <el-progress
      :percentage="progressPercentage"
      class="progress-bar"
      stroke-width="20"
      color="#67c23a"
      :text-inside="true"
    ></el-progress>

    <h2>{{ currentQuestion.text }}</h2>
    <div
      v-for="(option, index) in currentQuestion.options"
      :key="index"
      class="option-button"
    >
      <el-button
        type="success"
        @click="submitAnswer(option.value)"
        class="responsive-button"
      >
        {{ option.text }}
      </el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";
import { useRouter, useRoute } from "vue-router";
import { ElProgress } from "element-plus"; // Import progress component

const questions = [
  {
    text: "原本活潑親人的笑狗「果果」，突然對著你狂吠......",
    options: [
      { text: "塊陶阿~~~", value: "衛生" },
      { text: "撿起路邊的木棍......", value: "風紀" },
      { text: "從包包拿出昨天吃剩的巧克力誘惑牠", value: "康樂" },
      { text: "拉隔壁的同學當護盾", value: "班長" },
    ],
  },
  {
    text: "驚魂未定你的看到前面的糾察隊，你連忙看了一下自己，發現制服名號忘了繡......",
    options: [
      { text: "假裝沒事，邊吹著口哨邊通過糾察隊", value: "值日生" },
      { text: "從包包拿出昨天吃剩的巧克力賄賂糾察隊", value: "康樂" },
      { text: "撿起路邊的木棍......", value: "風紀" },
      { text: "穿上外套遮住制服", value: "學藝" },
    ],
  },
  {
    text: "終於來到熟悉的教室，發現班上來了個轉笑生，他似乎不小心坐到你的位子......",
    options: [
      {
        text: "展現你強大的親和力，跟他打招呼",
        value: "康樂",
      },
      {
        text: "尷尬地跟他說這是你的位子",
        value: "值日生",
      },
      {
        text: "坐在其他的位子，時不時瞄向對方",
        value: "學藝",
      },
      { text: "撿起路邊的木棍......", value: "風紀" },
    ],
  },
  {
    text: "升旗典禮開始，台上囉嗦的笑長嘮嘮叨叨講個不停......",
    options: [
      { text: "撿起路邊的木棍......", value: "風紀" },
      { text: "沒關係，再忍耐一下，安慰自己快結束了", value: "學藝" },
      { text: "假裝昏倒，順便躲進保健室睡個回籠覺", value: "衛生" },
      { text: "偷偷跟隔壁同學抱怨", value: "值日生" },
    ],
  },
  {
    text: "╰(〒皿〒)╯第一節居然是數學課！黑板上面的數字和符號好像在跳舞......",
    options: [
      { text: "從書包拿出漫畫偷看", value: "衛生" },
      { text: "撿起路邊的木棍......", value: "風紀" },
      { text: "和隔壁的同學傳紙條", value: "康樂" },
      {
        text: "繼續努力聽課！不能在這裡被打敗！",
        value: "學藝",
      },
    ],
  },
  {
    text: "午餐時間到了，但大雞腿和布丁的數量出了問題，排到你時剛好被前面同學拿走最後一份......",
    options: [
      {
        text: "學笑怎麼可以出這種問題，立刻跟老師反映",
        value: "班長",
      },
      { text: "和前面同學打個商量，一起平分", value: "康樂" },
      { text: "撿起路邊的木棍......", value: "風紀" },
      {
        text: " 算了今天就當減肥好了，不要吃那麼多",
        value: "值日生",
      },
    ],
  },
  {
    text: "下午去了福利社一趟！面對琳瑯滿目的商品......",
    options: [
      { text: "香甜的蘋果麵包，吃了下午上課才有精神", value: "學藝" },
      { text: "這麼熱的天氣，當然要來一瓶芭芒柳消消暑", value: "班長" },
      { text: "中午沒吃飽，買個肉羹麵邊上課邊吃", value: "康樂" },
      {
        text: "撿起路邊的木棍......",
        value: "風紀",
      },
    ],
  },
  {
    text: "終於放學了，今天跟同學約好一起去公園玩「鬼抓人」，但大家對這個遊戲的名稱爭論不休......",
    options: [
      { text: "這個遊戲叫做閃電BB", value: "班長" },
      { text: "這個遊戲叫做紅綠燈", value: "衛生" },
      { text: "就是鬼抓人啊？！", value: "值日生" },
      {
        text: "好麻煩喔～不想玩了",
        value: "值日生",
      },
    ],
  },
];

const route = useRoute();
const router = useRouter();
const questionIndex = computed(() => parseInt(route.params.questionIndex, 10));
const currentQuestion = computed(() => questions[questionIndex.value]);
const userName = computed(() => route.query.name);
const answers = ref({
  衛生: 0,
  康樂: 0,
  班長: 0,
  值日生: 0,
  學藝: 0,
  風紀: 0,
});

// 計算進度百分比
const progressPercentage = computed(() => {
  return ((questionIndex.value + 1) / questions.length) * 100;
});

const submitAnswer = (selectedValue) => {
  answers.value[selectedValue]++;

  if (questionIndex.value < questions.length - 1) {
    router.push({
      path: `/quiz/${questionIndex.value + 1}`,
      query: { name: userName.value, ...answers.value },
    });
  } else {
    router.push({
      path: "/result",
      query: { name: userName.value, ...answers.value },
    });
  }
};
</script>
<style scoped>
.background {
  background-image: url("../assets/background2.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: start;
  align-items: center;
  padding: 20px;
}

.progress-bar {
  width: 80%;
  margin-top: 30px;
}

h2 {
  text-align: center;
  margin-top: 30px;
  color: black;
  max-width: 90%;
  height: 200px; /* Fixed height for question text */
  display: flex;
  align-items: center; /* Center text vertically */
  justify-content: center; /* Center text horizontally */
  text-align: center;
  overflow: hidden; /* Hide overflow if question text is too long */
}

.option-button {
  display: flex;
  justify-content: center;
  width: 300px;
  max-width: 300px;
  margin-bottom: 10px;
}

.responsive-button {
  white-space: normal;
  word-wrap: break-word;
  width: 100%;
  height: 100%;
  display: block;
  line-height: 25px;
  border-radius: 30px;
}
</style>
