<template>
  <div class="background">
    <h2>
      聖誕國小新的學期要開始了，同學們紛紛懷著期待的心情來到笑門口，但今天的學笑似乎跟往常有點不同......
    </h2>
    <el-button
      type="success"
      @click="startQuiz"
      style="
        margin-top: 30px;
        width: 200px; 
        border-radius: 30px;
        height: 50px;
      "
      >踏入笑門</el-button
    >
  </div>
</template>

<script setup>
import { useRouter, useRoute } from "vue-router";
import { ElButton } from "element-plus";

const router = useRouter();
const route = useRoute();
const userName = route.query.name;

const startQuiz = () => {
  router.push({ path: "/quiz/0", query: { name: userName } });
};
</script>

<style scoped>
.background {
  background-image: url("../assets/background1.png"); /* 添加背景圖片 */
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  max-width: 90%;
}

.el-button {
  display: block;
  width: 100%;
  max-width: 300px;
  margin: 20px auto;
  font-size: 1.2em;
}
</style>
