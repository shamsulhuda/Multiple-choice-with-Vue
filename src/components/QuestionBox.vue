<template>
  <div class="questionbox-container">
    <b-jumbotron class="text-left">

      <template v-slot:lead>
        {{currentQuestion.question}}
      </template>

      <hr class="my-4">

      <b-list-group class="mb-4">
        <b-list-group-item button
        v-for="(answer, index) in answers"
        :key="index"
        @click="selectAnswer(index)"
        :class="[
        !answered && selectedIndex === index ? 'selected' :
        answered && correctIndex == index ? 'correct':
        answered && selectedIndex === index && correctIndex !== index ? 'incorrect':''
        ]"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>
      <b-button variant="success" href="javascript:void(0)" class="mr-2"
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered"
      >
        Submit
      </b-button>
      <b-button @click="next" variant="primary" href="javascript:void(0)">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from 'lodash'
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data: function() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    }

  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },

  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index
    },
    submitAnswer() {
      let isCorrect = false
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    }
  }
}

</script>

<style scoped>
  .selected {
    background-color: #EEE;
  }
  .correct {
    background-color: #03b700;
    color: #FFF;
  }
  .incorrect {
    background-color: #ff0000;
    color: #FFF;
  }
</style>
