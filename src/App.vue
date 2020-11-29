<template>
  <div id="app">
    <form class="decor" @submit.prevent="onSubmit()" style="width: 800px">
      <div class="form-left-decoration"></div>
      <div class="form-right-decoration"></div>
      <div class="circle"></div>
      <div class="form-inner">
        Вопрос<input type="text" name="questionText" v-model="questionText" />
        Ответы<textarea
          name="answers"
          v-model="answers"
          style="height: 350px"
        />
        Верный ответ<input type="text" name="correct" v-model="correct" />
        <input
          type="submit"
          :disabled="!questionText || !answers || !correct"
        />
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",

  data: () => ({
    questions: [],
    questionText: "",
    answers: "",
    correct: "",
    exist: null,
  }),

  methods: {
    async onSubmit() {
      if (!this.exist) {
        let formData = new FormData();
        formData.append("questionText", this.questionText);
        formData.append("answers", this.answers.split(";"));
        formData.append("correct", this.correct);
        formData.append("used", 0);

        const res = await fetch("http://directquiz.ru/questions", {
          method: "POST",
          body: formData,
        });

        const data = await res.json();

        console.log(data);

        this.questions.push({
          questionText: this.questionText,
          answers: this.answers,
          correct: this.correct - 1,
        });

        this.questionText = "";
        this.answers = "";
        this.correct = "";
      } else {
        alert("Такой вопрос уже существует");
      }
    },
  },

  watch: {
    questionText(val) {
      for (let question of this.questions) {
        if (question.questionText != val) {
          this.exist = false;
        } else {
          this.exist = true;
          break;
        }
      }
    },
  },

  mounted() {
    this.questions = axios
      .get("http://directquiz.ru/questions")
      .then((res) => (this.questions = res.data));
  },
};
</script>

<style>
* {
  box-sizing: border-box;
}
body {
  background: #f69a73;
}

input,
textarea {
  outline: none;
}

.decor {
  position: relative;
  margin: 50px auto 0;
  background: white;
  border-radius: 30px;
}
.form-left-decoration,
.form-right-decoration {
  content: "";
  position: absolute;
  width: 50px;
  height: 20px;
  background: #f69a73;
  border-radius: 20px;
}
.form-left-decoration {
  bottom: 60px;
  left: -30px;
}
.form-right-decoration {
  top: 60px;
  right: -30px;
}
.form-left-decoration:before,
.form-left-decoration:after,
.form-right-decoration:before,
.form-right-decoration:after {
  content: "";
  position: absolute;
  width: 50px;
  height: 20px;
  border-radius: 30px;
  background: white;
}
.form-left-decoration:before {
  top: -20px;
}
.form-left-decoration:after {
  top: 20px;
  left: 10px;
}
.form-right-decoration:before {
  top: -20px;
  right: 0;
}
.form-right-decoration:after {
  top: 20px;
  right: 10px;
}
.circle {
  position: absolute;
  bottom: 80px;
  left: -55px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: white;
}
.form-inner {
  padding: 50px;
}
.form-inner input,
.form-inner textarea {
  display: block;
  width: 100%;
  padding: 0 20px;
  margin-bottom: 10px;
  background: #e9eff6;
  line-height: 40px;
  border-width: 0;
  border-radius: 20px;
  font-family: "Roboto", sans-serif;
}
.form-inner input[type="submit"] {
  margin-top: 30px;
  background: #f69a73;
  border-bottom: 4px solid #d87d56;
  color: white;
  font-size: 14px;
}
.form-inner textarea {
  resize: none;
}
.form-inner h3 {
  margin-top: 0;
  font-family: "Roboto", sans-serif;
  font-weight: 500;
  font-size: 24px;
  color: #707981;
}
</style>
