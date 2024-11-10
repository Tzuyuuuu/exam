<template>
  <div class="background">
    <img :src="imageSrc" alt="image01" class="form-image" />
    
    <!-- Name Input Field -->
    <el-input
      v-model="name"
      placeholder="請輸入您的本名"
      class="name-input"
      clearable
    />

    <el-button type="success" @click="startQuiz" class="full-width-button">
      入學！
    </el-button>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import image01 from "../assets/image01.png";

const router = useRouter();
const imageSrc = image01;
const name = ref("");

const startQuiz = () => {
  const nameValue = name.value.trim();
  const isChineseName = /^[\u4e00-\u9fa5]{3}$/.test(nameValue); // checks for exactly 3 Chinese characters

  if (isChineseName) {
    router.push({ path: "/quiz", query: { name: nameValue } });
  } else {
    alert("請輸入您三個字的中文姓名(#`Д´)ﾉ");
  }
};

</script>

<style scoped>
.background {
  background-image: url("../assets/background1.png");
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.form-image {
  max-width: 190px;
  margin-top: 80px;
  display: block;
}

.full-width-button {
  margin-top: 30px;
  width: 200px;
  border-radius: 30px;
  height: 50px;
}

.name-input {
  margin-top: 20px;
  width: 200px;
}
</style>
