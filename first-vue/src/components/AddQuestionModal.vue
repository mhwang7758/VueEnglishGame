<template>
	<transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">

          <div class="title">
             <p>添加问题</p>
          </div>

          <div v-if="showWarning" class="warning">
             <p>{{wanrning}}</p>
          </div>

          <div class="qa">
             <!-- <input type="text" onkeyup="this.value=this.value.replace(/[^0-9-]+/,'')" name="question" placeholder="请输入编号" v-model="lastQNum" -->
             <input type="number" min="1" max="10" name="question" placeholder="请输入编号" v-model="lastQNum"
             class="question">
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
		name: "AddQuestionModal",

		data () {
			return {
        lastQNum: "",
        lastQuestion: "",
        lastAnswer: "",
        lastType: "qa",
        wanrning: "",
        showWarning: false
			}
		},

    methods:{
      onClickSure: function(){
        if(this.addQuestion()){
          this.addAnswer();
        }
        this.$emit('sure');
      },

      onClickCancel: function(){
        this.$emit('close');
      },

      addQuestion: function(){
        if (this.isEmpty(this.lastQNum)){
          this.wanrning = "请输入编号";
          this.showWarning = true;
          var that = this;
          this.hideWarning();
          return false;
        }

        if (this.isContain(this.lastQNum,'e')){
          this.wanrning = "编号请不要包含特殊字符";
          this.showWarning = true;
          this.hideWarning();
          return false;
        }

        if (this.isEmpty(this.lastQuestion)){
          this.wanrning = "请输入问题";
          this.showWarning = true;
          this.hideWarning();
          return false;
        }

        if (this.isEmpty(this.lastAnswer)){
          this.wanrning = "请输入答案";
          this.showWarning = true;
          return false;
        }

        var question = {};
        question["question"] = this.lastQuestion;
        question["qNum"] = this.lastQNum;
        question["type"] = this.lastType;
        axios.post(QUESTION_URL, question)
             .then(response=>{
                console.log(response);
              })
             .catch(function (error){
                console.log(error)
             });
        return true;
      },

      addAnswer: function(){
        var answer = {};
        answer["qNum"] = this.lastQNum;
        answer["answer"] = this.lastAnswer;
        axios.post(ANSWER_URL, answer)
             .then(response=>{
                console.log(response);
              })
             .catch(function (error){
                console.log(error)
             })
      },

      isEmpty: function(str){
        if (typeof str == "undefined" || str == null || str == ""){
          return true;
        }
          return false;
      },

      isContain: function(str, c){
        return str.indexOf(c) != -1;
      },

      hideWarning: function(){
        var that = this;
        setTimeout(function(){
            console.log("hide warning")
            that.showWarning = false;
          }, 2000);
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
  text-align: center;
  font-size: 30px;
  color: black;
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

.warning{
  font-size: 20px;
  color: red;
  text-align: center;
}

</style>