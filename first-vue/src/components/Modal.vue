<template>
	<transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">

          <div class="title">
             <p class="question-id">编号：{{modifyQuestion.qNum}}</p>
             <p class="label">已回答？</p>
             <img :src="'../../static/'+imgPath" @click="changeChecked()" class="check">
          </div>

          <div class="qa">
             <input type="text" name="question" placeholder="请输入问题" v-model="lastQuestion"
             class="question">
             <input type="text" name="answer" placeholder="请输入答案" v-model="lastAnswer"
             class="answer">
          </div>

          <div class="footer">
            <button class="button left" @click="onClickCancel()">
              取消
            </button>

            <button class="button right" @click="onClickSure()">
              确定
            </button>

          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import axios from 'axios'
  const QUESTION_URL = 'http://127.0.0.1:9999/questions';
  const ANSWER_URL = 'http://127.0.0.1:9999/answers';

	export default{
		name: "Modal",

    props: {
      question: Object,
    },

		data () {
			return {
				modifyQuestion: this.question,
        answer: null,
        imgPath: this.question.answered ? 'ic_checked.png' : 'ic_unchecked.png',
        lastQuestion: this.question.question,
        lastAnswer: '没有答案'
			}
		},


    mounted: function(){
      // 查找问题答案
      axios.get(ANSWER_URL+'/'+this.modifyQuestion.qNum)
      .then(response=>{
        this.answer = response.data.datas.answers[0]
        this.lastAnswer = this.answer.answer
      })
      .catch(function (error){
          console.log(error)
        })
    },

    methods:{
      changeChecked: function(){
        this.modifyQuestion.answered = !this.modifyQuestion.answered;
        this.imgPath = this.modifyQuestion.answered ? 'ic_checked.png' : 'ic_unchecked.png'
      },

      onClickSure: function(){
        this.updateQuestion();
        this.updateAnswer();
        this.$emit('sure');
      },

      onClickCancel: function(){
        this.$emit('close');
      },

      updateQuestion: function(){
        this.modifyQuestion.question = this.lastQuestion;
        axios.put(QUESTION_URL, this.modifyQuestion)
             .then(response=>{
                console.log(response);
              })
             .catch(function (error){
                console.log(error)
             })
      },

      updateAnswer: function(){
        this.answer.answer = this.lastAnswer;
        axios.put(ANSWER_URL, this.answer)
             .then(response=>{
                console.log(response);
              })
             .catch(function (error){
                console.log(error)
             })
      }

	  }
  }
</script>

<style scoped>

.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 500px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

.text{
  margin-left: auto;
  margin-right: auto;
  font-size: 35px;
}

.title{
  display: flex;
  flex-direction: row;
  align-items: center;
}

.title .question-id{
  font-size: 35px;
  margin-left: 50px;
}

.title .label{
  font-size: 35px;
  margin-left: 50px;
}

.title .check{
  width: 60px;
  height: 60px;
}

.qa{
  display: flex;
  flex-direction: column;
}

.qa .question{
  height: 50px;
  font-size: 20px;
}

.qa .answer{
  height: 50px;
  margin-top: 20px;
  font-size: 20px;
}

.footer{
  display: flex;
  flex-direction: row;
  position: relative;
  height: 50px;
  margin-top: 20px;
}

.button{
  width: 100px;
  height: 50px;
  font-size: 20px;
  border-radius: 5px;
  background: #ff3300;
}

.button:active{
  width: 100px;
  height: 50px;
  font-size: 20px;
  border-radius: 5px;
  background: #ff3399;
}

.left{
  position: absolute;
  left: 50px;
}

.right{
  position: absolute;
  right: 50px;
}

</style>