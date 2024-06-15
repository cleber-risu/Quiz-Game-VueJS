<template>
  <div>

    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />

    <template v-if="this.answers">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" :key="index">
        <input type="radio" name="options" :value="answer" :disabled="this.answerSubmited" v-model="this.chosenAnswer">
        <label v-html="answer"></label>
        <br>
      </template>

      <button class="send" type="button" v-if="!this.answerSubmited" @click="this.submitAnswer()">Send</button>

      <section class="result" v-if="this.answerSubmited">
        <h4 v-if="this.chosenAnswer === this.correctAnswer"
          v-html="'&#9989; Congratulations, the answer ' + this.correctAnswer + ' is Correct.'">
        </h4>
        <h4 v-else>
          &#10060; I'm sorry, you picked the wrong answer. The correct is "<span v-html="this.correctAnswer"></span>".
        </h4>
        <button @click="this.getNewQuestion()" class="send" type="button" v-if="this.chosenAnswer">
          Next question
        </button>
      </section>

    </template>

  </div>
</template>

<script>

import ScoreBoard from './components/ScoreBoard.vue';

export default {
  name: 'App',
  components: {
    ScoreBoard
  },

  data() {
    return {
      question: undefined,
      incorrectAnswers: [],
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmited: false,
      winCount: 0,
      loseCount: 0,
    }
  },

  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('Pick on of the options!');
      } else {
        this.answerSubmited = true;
        if (this.chosenAnswer === this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },

    getNewQuestion() {
      this.answerSubmited = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios
        .get('https://opentdb.com/api.php?amount=1&category=18')
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
          console.log(response.data.results[0])
        })
    }
  },

  computed: {
    answers() {
      let answers = [...this.incorrectAnswers];
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
      return answers;
    }
  },

  created() {
    this.getNewQuestion()
  }
}

// https://opentdb.com/api.php?amount=1&category=18
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

    color: #fff;

    border: 1px solid #1867c0;
    background: #1867c0;
    cursor: pointer;
  }
}
</style>
