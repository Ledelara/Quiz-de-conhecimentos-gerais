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

      <button v-if="!answerSubmited" class="send" type="button" @click="submitAnswer()">Send</button>

      <section v-if="answerSubmited" class="result">
        <h4 v-if="chosenAnswer == correctAnswer" v-html="`&#9989; Congratulations, the answer ${correctAnswer} is correct!`"></h4>

        <h4 v-else v-html="`&#10060; I'm sorry, you picked the wrong answer. The correct answer is: ${correctAnswer}`"></h4>
        <button class="send" type="button" @click="getNewQuestion()">Next question</button>
      </section>
    </template>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from 'axios'
import ScoreBoard from './components/ScoreBoard.vue'

export default defineComponent({
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      question: '' as string,
      incorrectAnswers: [] as string[],
      correctAnswer: '' as string,
      chosenAnswer: '' as string,
      answerSubmited: false as boolean,
      winCount: 0 as number,
      loseCount: 0 as number
    }
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers))
      answers.splice(Math.floor(Math.round(Math.random() * answers.length)), 0, this.correctAnswer)
      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if(!this.chosenAnswer) {
        alert('Please select an answer!')
      } else {
        this.answerSubmited = true
        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },

    getNewQuestion() {
      this.answerSubmited = false
      this.chosenAnswer = ''
      this.question = ''
      this.incorrectAnswers = []
      this.correctAnswer = ''

      axios.get('https://opentdb.com/api.php?amount=1')
      .then((response: any) => {
        const result = response.data.results[0]
        this.question = result.question
        this.incorrectAnswers = result.incorrect_answers
        this.correctAnswer = result.correct_answer
      })
    }
  },
  created() {
    this.getNewQuestion()
  }
})
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

  input[type='radio'] {
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
