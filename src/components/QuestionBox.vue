<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        {{ currentQuestion.question }}
      </template>
      <hr class="my-4"/>
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>
      <hr class="my-4" />
      <b-button variant="primary" @click="submitAnswer" :disabled="choixAnswer.length === 0 || this.answered" >Submit</b-button>
      <b-button @click="next" variant="success">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  //
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      answered:false,
      shuffledAnswers: [],
      choixAnswer: []
    };
  },
  watch: {
    // Le watch te permet ici de renitialiser les valeurs a chaque changement de la question c-a-d quand on clique sur le next
    currentQuestion: {
      immediate: true,
      handler() {
        this.shuffledAnswers= [],
        this.choixAnswer= [],
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      for (let i = 0; i < this.currentQuestion.correct_answer.length; i++) {
        answers.push(this.currentQuestion.correct_answer[i]);
      }
      return _.shuffle(answers);
    },
  },

  methods:{
    shuffleAnswers() {
      for (let i = 0; i < this.answers.length; i++) {
        for (let j = 0; j < this.currentQuestion.correct_answer.length; j++) {
          if (
            this.answers[i].includes(this.currentQuestion.correct_answer[j])
          ) {
            this.shuffledAnswers.push(this.answers[i]);
          }
        }
      }
      console.log(this.shuffledAnswers);
    },

    selectAnswer(i){
    if(this.choixAnswer.indexOf(this.answers[i]) === -1){
      this.choixAnswer.push(this.answers[i]);
    }
        // console.log(this.choixAnswer);
    },

    submitAnswer() {
    let isCorrect= false
    this.answered = true;
    let cmp = 0
    if (this.choixAnswer.length == this.shuffledAnswers.length)
    {
      for (let i = 0; i < this.choixAnswer.length; i++){
        for (let j = 0; j < this.shuffledAnswers.length; j++){
          if (this.choixAnswer[i].includes(this.shuffledAnswers[j])) {
            cmp++
          }
        }
      }console.log(cmp);
      if(this.choixAnswer.length !== cmp){
        isCorrect = false
      }else
        isCorrect= true
        this.increment(isCorrect);

    }else 
      this.increment(isCorrect)
    },

    answerClass(index) {
    let currentSelection=  this.answers[index];
    let answerClass='';
    for (let i = 0; i < this.choixAnswer.length; i++){
      if(!this.answered && this.choixAnswer[i] === currentSelection){
        answerClass = 'selected'
      }/**************************************************** */
    }
    if(this.answered)
      
        if(this.shuffledAnswers.includes(currentSelection) ){
          answerClass = 'correct';
        }
        else if(!this.shuffledAnswers.includes(currentSelection) && this.choixAnswer.includes(currentSelection)) {
          answerClass = 'incorrect';
        }
  
    // console.log(this.shuffledAnswers);
    // console.log(this.choixAnswer);
    return answerClass;
  }

  }

};
</script>

<style scoped>
.list-group {
  margin-bottom: 5px;
}
.list-group-item:hover {
  background: rgb(241, 241, 241);
  cursor: pointer;
}
.btn {
  margin: 0 15px;
}
.selected {
  background-color: rgb(213, 231, 255);
}
.correct {
  background-color: rgb(157, 255, 157);
}
.incorrect {
  background-color: rgb(255, 153, 153);
}
</style>
