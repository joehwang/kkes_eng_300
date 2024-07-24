<template>
  <div class="" id="app">
    <h2 class="text-4xl mb-1">⭐️ Score: {{ score }}</h2>
    <Quiz
      v-if="currentQuestionIndex < questions.length"
      :question="questions[currentQuestionIndex]"
      :checkAnswer="checkAnswer"
    />

    <div v-else>
      <h2>Quiz finished! Your final score is: {{ score }}</h2>
      <button @click="resetQuiz">Restart Quiz</button>
    </div>
    <div>
      <h2>答錯題目</h2>
      <div>{{ wrongs_words.join(",") }}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Quiz from "./components/Quiz.vue";

export default {
  name: "App",
  components: {
    Quiz,
  },
  data() {
    return {
      score: 0,
      currentQuestionIndex: 0,
      questions: [],
      wrongs_words: [],
    };
  },
  async created() {
    this.questions = [
      ...(await this.loadQuestions("/eng.json")),
      ...(await this.loadQuestions("/ch.json")),
    ];
    this.questions.sort(() => 0.5 - Math.random());
  },
  methods: {
    async loadQuestions(_json) {
      let result = [];
      try {
        const response = await axios.get(_json);
        const data = response.data;

        const randomItems = this.getRandomItems(data, 300);
        result = randomItems.map((item) => {
          const { english_word, chinese_word } = item;
          const options = [english_word];

          while (options.length < 4) {
            const option = this.getRandomOption(data);
            if (!options.includes(option)) {
              options.push(option);
            }
          }
          this.shuffleArray(options);

          return {
            text: chinese_word,
            options: options,
            correctIndex: options.indexOf(english_word),
          };
        });

        return result;
      } catch (error) {
        console.error("Error loading questions:", error);
      }
    },
    getRandomItems(data, count) {
      const items = Object.entries(data).map(([key, value]) => ({
        english_word: key,
        chinese_word: value,
      }));
      const shuffled = items.sort(() => 0.5 - Math.random());
      return shuffled.slice(0, count);
    },
    getRandomOption(data) {
      const keys = Object.keys(data);
      const randomKey = keys[Math.floor(Math.random() * keys.length)];
      return randomKey;
    },
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },
    incrementScore() {
      this.score++;
    },
    decrementScore() {
      this.score -= 6;
    },
    checkAnswer(index) {
      if (index === this.questions[this.currentQuestionIndex].correctIndex) {
        this.incrementScore();
      } else {
        this.decrementScore();
        this.wrongs_words.push(this.questions[this.currentQuestionIndex].text);
      }
      this.nextQuestion();
    },
    nextQuestion() {
      if (this.currentQuestionIndex < this.questions.length - 1) {
        this.currentQuestionIndex++;
      } else {
        alert(`Quiz finished! Your final score is: ${this.score}`);
      }
    },
    resetQuiz() {
      this.currentQuestionIndex = 0;
      this.score = 0;
      this.loadQuestions();
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #fff;
  min-width: 45vw;
  background-color: dimgrey;
}
</style>
