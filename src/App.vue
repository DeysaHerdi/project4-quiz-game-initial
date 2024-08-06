<template>
  <div>

    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount"/>

    <template v-if="this.question">
      <h1 v-html="this.question"></h1>

      <template v-for="(answer, index) in this.answers" v-bind:key="index">
        <input
        :disabled="this.answerSubmitted"
        type="radio" 
        name="options" 
        :value= "answer"
        v-model="this.chosenAnswer"
        >

        <label v-html="answer"></label><br>
        
      </template>

      <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>
    
      <section class="result" v-if="this.answerSubmitted">
        <h4 v-if="this.chosenAnswer === this.correctAnswer" v-html="'&#9989; Parabens, voce acertou xD. A resposta correta é ' + this.correctAnswer + '.'">
        </h4> 
        <h4 v-else 
        v-html="'&#10060; Sinto muito mas você errou xD. A resposta correta é ' + this.correctAnswer + '.'">
        </h4>
        <button  @click="this.getNewQuestion()" class="send" type="button">Nova Pergunta</button>
      </section>

    </template>
  </div>
</template>


<script>
import ScoreBoard from './components/ScoreBoard.vue'

export default {
  name: 'App',
  components: {
    ScoreBoard
  },

  data() {
    return {
      question: '',
      incorrectAnswers: [],
      correctAnswer: '',
      chosenAnswer: '',
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },
  computed: {
    answers() {
      var answers = [...this.incorrectAnswers]
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer)
      return answers
    } 
  },
  methods: {
    submitAnswer() {
      if(!this.chosenAnswer){
      console.log("escolha um xD")  
      }else{
        this.answerSubmitted = true
        if(this.chosenAnswer == this.correctAnswer) {
          this.winCount++
        }else{
          this.loseCount++
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false
      this.chosenAnswer = ''
      this.question = ''

      this.axios
      .get("https://opentdb.com/api.php?amount=1&category=18")
      .then((response) => {
        this.question = response.data.results[0].question
        this.incorrectAnswers = response.data.results[0].incorrect_answers
        this.correctAnswer = response.data.results[0].correct_answer
      })
    }
  },

  created() {
    this.getNewQuestion()
  }
}
//https://opentdb.com/api.php?amount=1&category=18
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px auto;
  max-width: 960px;
  
  input[type=radio] {
      margin: 12px 4px;
  }

  button.send{
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

/*
https://opentdb.com/api.php?amount=1&category=18


(objeto) => objeto
(objeto) => mapa => objeto
id => ID

const correctAnswer = resposta.results[0].correct_answer
{
  "response_code": 0,
  "results": [
    {
      "type": "multiple",
      "difficulty": "easy",
      "category": "Science: Computers",
      "question": "Which company was established on April 1st, 1976 by Steve Jobs, Steve Wozniak and Ronald Wayne?",
      "correct_answer": "Apple",
      "incorrect_answers": [
        "Microsoft",
        "Atari",
        "Commodore"
      ]
    }
  ]
}

*/