<template>
  <div>
    <ScoreBoard :playerScore="winCount" :computerScore="loseCount" />

    <template v-if="question">
      <h1 v-html="question"></h1>

      <template v-for="answer in answers" :key="answer">
        <input
          :disabled="answerSubmited"
          type="radio"
          name="options"
          :value="answer"
          v-model="chosenAnswer"
        />
        <label>{{ answer }}</label><br />
      </template>

      <button
        v-if="!answerSubmited"
        class="send"
        type="button"
        @click="submitAnswer()"
      >
        Send
      </button>

      <section v-if="answerSubmited" class="result">
        <h4
          v-if="chosenAnswer === correctAnswer"
          v-html="`&#9989; Congratulations, the answer ${correctAnswer} is correct!`"
        ></h4>

        <h4
          v-else
          v-html="`&#10060; I'm sorry, you picked the wrong answer. The correct answer is: ${correctAnswer}`"
        ></h4>
        <button class="send" type="button" @click="getNewQuestion()">Next question</button>
      </section>
    </template>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";
import ScoreBoard from "./components/ScoreBoard.vue";

interface TriviaQuestion {
  category: string;
  type: string;
  difficulty: string;
  question: string;
  correct_answer: string;
  incorrect_answers: string[];
}

interface TriviaApiResponse {
  response_code: number;
  results: TriviaQuestion[];
}

export default defineComponent({
  name: "App",
  components: {
    ScoreBoard,
  },
  data() {
    return {
      question: "" as string,
      incorrectAnswers: [] as string[],
      correctAnswer: "" as string,
      chosenAnswer: "" as string,
      answerSubmited: false as boolean,
      winCount: 0 as number,
      loseCount: 0 as number,
    };
  },
  computed: {
    answers(): string[] {
      // Clonando o array para não modificar o original
      const answers = [...this.incorrectAnswers];
      // Inserindo a resposta correta em uma posição aleatória
      const randomIndex = Math.floor(Math.random() * (answers.length + 1));
      answers.splice(randomIndex, 0, this.correctAnswer);
      return answers;
    },
  },
  methods: {
    submitAnswer(): void {
      if (!this.chosenAnswer) {
        alert("Please select an answer!");
        return;
      }

      this.answerSubmited = true;
      if (this.chosenAnswer === this.correctAnswer) {
        this.winCount++;
      } else {
        this.loseCount++;
      }
    },

    getNewQuestion(): void {
      this.answerSubmited = false;
      this.chosenAnswer = "";
      this.question = "";
      this.incorrectAnswers = [];
      this.correctAnswer = "";

      axios
        .get<TriviaApiResponse>("https://opentdb.com/api.php?amount=1")
        .then((response) => {
          const result = response.data.results[0];
          this.question = result.question;
          this.incorrectAnswers = result.incorrect_answers;
          this.correctAnswer = result.correct_answer;
        })
        .catch((error) => {
          console.error("Erro ao buscar pergunta:", error);
        });
    },
  },
  created() {
    this.getNewQuestion();
  },
});
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type="radio"] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
