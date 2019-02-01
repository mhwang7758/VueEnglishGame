
<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1> -->
    <div v-for="(question,index) in questions" :key="question.id" class="item">
      <p class="text">{{question.question}}</p>
      <div class="operation">
        <button class="button" @click="showModal(question)">修改</button>
        <button class="button" @click="deleteItem(index)">删除</button>
      </div>
    </div>

    <button @click="showAddQuestion()">添加问题</button>
    <modal v-if="modalShow" @close="hideModal()" v-bind:question="modifyQuestion"></modal>
    <AddQuestionModal v-if="addQuestionModalShow" @close="hideQuestionModal()"></AddQuestionModal>
  </div>
</template>

<script>

import modal from './Modal.vue'
import AddQuestionModal from './AddQuestionModal.vue'
import axios from 'axios'

const QUESTION_URL = 'http://127.0.0.1:9999/questions/delete/0';

export default {
  components:{
    modal,
    AddQuestionModal
  },

  name: 'HelloWorld',

  data () {
    return {
      msg: 'are you ok?',
      modalShow: false,
      addQuestionModalShow: false,
      modifyQuestion: null,
      questions: [],
      datas: "null"
    }
  },

  created: function(){
    this.getQuestions();
  },

  methods:{
    deleteItem: function(index){
      this.questions.splice(index, 1);
    },
    showModal: function(question){
      console.log("showModal")
      this.modalShow = !this.modalShow;
      this.modifyQuestion = question;
    },
    hideModal: function(){
      this.modalShow = false;
    },
    showAddQuestion: function(){
      this.addQuestionModalShow = true;
    },
    hideQuestionModal: function(){
      this.addQuestionModalShow = false;
    },
    getQuestions: function(){
      axios.get(QUESTION_URL)
           .then(response=>{
            this.questions = response.data.datas.questions;
           })
           .catch(function(error){
            console.log(error);
           })
    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .item{
    margin-top: 5px;
    display: flex;
    height: 60px;
    background: gray; 
  }

  .item .text{
    text-align: center;
    margin: auto;
    font-size: 25px;
  }

  .item .operation{
    position: absolute;
    background: gray; 
    height: 60px;
    display: flex;
    flex-direction: row;
    right: 0px;
  }

  .item .operation .button{
    background: red;
    width: 200px;
    font-size: 30px;
  }

  .item .operation .button:active{
    background: blue;
    width: 200px;
    font-size: 30px;
  }
</style>
