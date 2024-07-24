<template>
  <div class="quiz">
    <h1 class="text-8xl text-center text-white bold mb-3">
      {{ question.text }}
    </h1>
    <ul>
      <li v-for="(option, index) in question.options" :key="index">
        <button @click="handleClick(index)">{{ option }}</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  props: ["question", "checkAnswer"],
  data() {
    return {
      correct_audio: new Audio("/sounds/correct.m4a"), // 确保路径正确
    };
  },
  methods: {
    handleClick(index) {
      this.playSound();
      this.checkAnswer(index);
    },
    playSound() {
      this.correct_audio.play();
    },
  },
};
</script>

<style scoped>
.quiz {
  text-align: left;
}
li {
  list-style-type: number;
}
button {
  margin: 0.4em;
  cursor: pointer;
}
</style>
