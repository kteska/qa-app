<template>
  <div>
    <nav>
      <v-app-bar color="white" class="elevation-0 app-bar">
        <v-toolbar-title style="font-family: 'Sorts Mill Goudy', serif; font-size: 25px">QA</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn class="elevation-0 nav-button" color="transparent" to="/"> Home </v-btn>
        <v-btn class="elevation-0 nav-button" color="transparent" to="/ask"> Ask </v-btn>
        <v-btn class="elevation-0 nav-button" color="transparent"> About </v-btn>
      </v-app-bar>
    </nav>

    <div class="question-form">
      <div>
        <v-card width="660" height="757" class="elevation-6 card-form" tile>
          <v-card-title class="display-2">Ask a question</v-card-title>
          <v-card-text>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-text-field color="extra" v-model="name" :counter="20" :rules="nameRules" label="Name" required></v-text-field>

              <v-text-field v-model="question" color="extra" auto-grow label="Enter your question here..." :counter="100" :rules="questionRules" required> </v-text-field>

              <v-select v-model="selectedCategory" :items="categories" color="bluey" chips attach :rules="[(v) => !!v || 'You have to choose category']" label="Category" required> </v-select>

              <v-btn dark color="extra" class="mr-4" @click="submitQuestion(question, name, selectedCategory)"> Submit </v-btn>
            </v-form>
          </v-card-text>
          <v-alert :value="alert" dense text type="success"> Your question has been <strong>saved!</strong> Now let's wait for <strong>answers!</strong> </v-alert>
          <!-- <v-alert :value="alert" dense text type="error">{{ this.errorMessage }}</v-alert> -->
        </v-card>
      </div>
      <div>
        <v-img src="../assets/yacht.jpg" width="840" height="757"></v-img>
      </div>
    </div>
  </div>
</template>

<script>
import { addQuestion } from "../api/api";
export default {
  name: "QuestionForm",
  data: () => ({
    alertError: false,
    errorMessage: "",
    alert: false,
    valid: false,
    name: "",
    nameRules: [(v) => !!v || "Name is required", (v) => (v && v.length <= 20) || "Name must be less than 20 characters"],
    question: "",
    questionRules: [(v) => !!v || "Question is required", (v) => (v && v.length <= 100) || "Name must be less than 100 characters"],
    selectedCategory: null,
    categories: ["Sailing", "Marinas", "Weather", "Equipment"],
    checkbox: false,
  }),
  methods: {
    submitQuestion(question, name, selectedCategory) {
      let category = 0;
      switch (selectedCategory) {
        case "Sailing":
          category = 0;
          break;
        case "Marinas":
          category = 1;
          break;
        case "Weather":
          category = 2;
          break;
        default:
          category = 3;
          break;
      }
      this.$refs.form.validate();
      if (!this.valid) {
        return;
      } else {
        addQuestion({ title: question, username: name, category: category })
          .then((res) => {
            console.log(res);
            // if (res.startsWith("Missing")) {
            //   this.alertError = true;
            //   setTimeout(() => {
            //     this.alertError = false;
            //     console.log("error", res);
            //     this.errorMessage = res;
            //   }, 3000);
            // } else {
            this.alert = true;
            this.$refs.form.reset();
            this.$refs.form.resetValidation();
            setTimeout(() => {
              this.alert = false;
            }, 4000);
          })
          .catch((err) => {
            this.alertError = true;
            setTimeout(() => {
              this.alertError = false;
              console.log("error", err);
              this.errorMessage = err;
            }, 3000);
          });
      }
    },
  },
};
</script>
<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Sorts+Mill+Goudy&display=swap");
.questionform-card {
  margin-top: 50px;
}
.question-form {
  display: flex;
  justify-content: stretch;
  align-items: stretch;
}
.card-form {
  padding: 50px;
  padding-top: 140px;
  z-index: 5;
}
</style>
