<template>
  <div class="container">
    <div ref="resultArea" v-if="result">
      <!-- Greeting Message -->
      <div v-if="userName" class="greeting">
        <p>{{ userName }}同學！這是您的測驗結果(・`ω´・)</p>
      </div>

      <!-- Image Container with Name Overlay -->
      <div class="image-container">
        <img :src="resultImage" alt="測驗結果圖片" class="result-image" />
        <div class="overlay-name">{{ userName }}</div>
      </div>

      <!-- Teacher Comment, Secret Mission, Tribal Mission, etc. -->
      <div class="tag-container">
        <span class="tag-label">
          <font-awesome-icon :icon="['fas', 'pen']" class="icon-small" />
          老師評語
        </span>
      </div>
      <div class="info-block">
        <p>{{ teacherComment }}</p>
      </div>

      <!-- Secret and Tribal Missions as before -->
      <div v-if="secretMission" class="tag-container">
        <span class="tag-label">
          <font-awesome-icon :icon="['fas', 'envelope']" class="icon-small" />
          秘密任務
        </span>
      </div>
      <div v-if="secretMission" class="info-block">
        <p v-html="secretMission"></p>
      </div>
      <div v-if="tribalMission" class="tag-container">
        <span class="tag-label">
          <font-awesome-icon
            :icon="['fas', 'puzzle-piece']"
            class="icon-small"
          />
          九族任務
        </span>
      </div>
      <div v-if="tribalMission" class="info-block">
        <p>{{ tribalMission }}</p>
      </div>
    </div>

    <!-- Download and Retry Buttons -->
    <div>
      <!-- <el-button type="success" class="download-button"> -->
        <span style="font-weight: 900; font-size: 20px; ">⬆ 截圖回傳給校長 (ﾉ>ω<)ﾉ ⬆</span>
      <!-- </el-button> -->
    </div>
    <div>
      <el-button type="success" @click="retryQuiz" class="download-button">
        再玩一次（請回傳第一次結果！）
      </el-button>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, nextTick } from "vue";
import { useRouter, useRoute } from "vue-router";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import {
  faPen,
  faEnvelope,
  faPuzzlePiece,
} from "@fortawesome/free-solid-svg-icons";
import { library } from "@fortawesome/fontawesome-svg-core";

// Add icons to the FontAwesome library
library.add(faPen, faEnvelope, faPuzzlePiece);

// Import assets
import result1 from "../assets/result1.png";
import result2 from "../assets/result2.png";
import result3 from "../assets/result3.png";
import result4 from "../assets/result4.png";
import result5 from "../assets/result5.png";
import result6 from "../assets/result6.png";

const route = useRoute();
const router = useRouter();
const userName = computed(() => route.query.name);
const answerCounts = {
  班長: parseInt(route.query.班長) || 0,
  衛生: parseInt(route.query.衛生) || 0,
  康樂: parseInt(route.query.康樂) || 0,
  值日生: parseInt(route.query.值日生) || 0,
  學藝: parseInt(route.query.學藝) || 0,
  風紀: parseInt(route.query.風紀) || 0,
};
console.log(userName.value);

const result = computed(() => {
  let maxCount = 0;
  let maxRole = "";

  // 檢查是否為「游采臻」或「張致瑜」，如果是，則跳過「風紀」的計算
  if (userName.value === "游采臻" || userName.value === "張致瑜") {
    // 過濾出除「風紀」之外的角色，並找到最高分
    const filteredRoles = Object.entries(answerCounts).filter(
      ([role]) => role !== "風紀"
    );

    // 計算分數最高的角色
    filteredRoles.forEach(([role, count]) => {
      if (count > maxCount) {
        maxCount = count;
        maxRole = role;
      }
    });
  } else {
    // 非受限名稱的情況下進行正常判斷
    if (answerCounts.風紀 >= 3) {
      return "風紀";
    }

    // 找出最高分的角色
    for (const [role, count] of Object.entries(answerCounts)) {
      if (count > maxCount) {
        maxCount = count;
        maxRole = role;
      }
    }
  }

  return maxRole;
});

const resultImage = computed(() => {
  switch (result.value) {
    case "班長":
      return result1;
    case "風紀":
      return result2;
    case "學藝":
      return result3;
    case "衛生":
      return result4;
    case "康樂":
      return result5;
    case "值日生":
      return result6;
    default:
      return ""; // No placeholder needed for final version
  }
});

// Define comments, secret missions, and tribal missions for each role
const teacherComment = computed(() => {
  switch (result.value) {
    case "康樂":
      return "康樂忙，氣氛揚，打球跑步樣樣強，歌聲揚，笑聲響，班級活力他最棒。體育課負責帶大家做操，lucy比賽第一名！";
    case "班長":
      return "班長領導人人敬，安排妥當又分明，同學有事他先頂，全班和諧笑聲盈！大部分是班上投票出來的人氣王，但偶爾也可能是被指派的倒霉鬼QQ";
    case "風紀":
      return "負責管理班上秩序，通常由班上最機車的同學擔任，有時候老師也會反其道而行，指派秩序最差的同學來負責！總之希望畢旅這晚不會被檢舉QQ";
    case "學藝":
      return "通常是乖乖牌，字寫得很漂亮！就像是為了教室佈置大賽而生的股長！學藝股長有創意，佈置牆報真得意，手巧心靈又細膩，讓班級更有新意！";
    case "衛生":
      return "負責安排大家打掃的工作內容，結果最後都變成自己在做QQ愛整潔，重清潔，桌面乾淨人人學，地板亮，窗明潔，舒心環境無人怯！";
    case "值日生":
      return "值日忙，掃地忙，角角落落掃得光，桌椅正，地板亮，乾淨教室人人誇！每個人都有機會輪到，雖然不起眼，但可是班上不可或缺的重要角色！";
    default:
      return "";
  }
});

const secretMission = computed(() => {
  switch (result.value) {
    case "康樂":
      return "11/17 早上負責帶自己班上的同學做早操，記得錄影存檔哦！";
    case "班長":
      return "期末考校排前三名可以額外加分";
    case "風紀":
      return "1.看到隔壁班同學觸犯笑龜立刻拍照回傳給主任！<br>2.抓班上的間諜！間諜同學可能會惡意觸犯笑龜！";
    case "衛生":
      return "1.值日生一起整理環境<br>2.小遊戲獲勝獲得額外加分";
    case "值日生":
      return "1.協助衛生股長一起整理環境<br>2.小遊戲獲勝獲得額外加分";
    default:
      return null;
  }
});

const tribalMission = computed(() => {
  switch (result.value) {
    case "康樂":
      return "勇闖瑪雅聽說超刺激～康樂股長應該要帶頭衝啊！";
    case "班長":
      return "拍五張團體合照回傳給校長";
    case "學藝":
      return "這次的功課是在九族找到「排灣族百步蛇圖騰」、「達悟族的拼板船」、「卑南族盪鞦韆」、「魯凱族的石板屋」、「布農族雞舍」，然後跟它們一起合照回傳！";
    default:
      return null;
  }
});

const resultArea = ref(null);
const retryQuiz = () => {
  router.push("/");
};
</script>

<style scoped>
.container {
  background-image: url("../assets/background2.png");
  background-size: cover;
  background-position: center;
  padding: 20px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.image-container {
  position: relative;
  width: 100%;
  max-width: 400px;
  margin-top: 20px;
  margin-bottom: 20px;
  border-radius: 15px;
}

.result-image {
  width: 100%;
  height: auto;
  display: block;
}

.info-block {
  background-color: #49754f;
  padding: 10px;
  margin-bottom: 20px;
  width: 100%;
  max-width: 400px;
  text-align: center;
  border-radius: 0 8px 8px 8px;
  color: white;
}

.overlay-name {
  position: absolute;
  top: 172px; /* Adjust the position as needed */
  left: 52px;
  font-size: 20px;
  font-weight: bold;
  color: #e39f9a;
}
.tag-container {
  display: flex;
  justify-content: start;
}

.greeting {
  font-size: 20px;
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

.tag-label {
  background-color: #e39f9a;
  color: white;
  padding: 4px 8px;
  font-size: 16px;
  font-weight: 900;
  border-radius: 8px 8px 0 0;
  display: flex;
  align-items: center;
}

.icon-small {
  margin-right: 10px;
  font-size: 16px;
}

.download-button {
  border-radius: 30px;
  width: 300px;
  height: 50px;
  margin-top: 20px;
}
</style>
