<template>
  <div class="submit-form mw-100 w-50">
    <div class="border border-dark shadow-lg p-3 mb-5 bg-white rounded col-md-12" v-if="!submitted">
      <div class="form-group">
        <label for="title">Titulo</label>
        <input
          type="text"
          class="form-control"
          id="title"
          required
          v-model="tutorial.title"
          name="title"
        />
      </div>
  <br/>
      <div class="form-group">
        <label for="description">Descripcion</label>
        <input
          class="form-control"
          id="description"
          required
          v-model="tutorial.description"
          name="description"
        />
      </div>
      <p style="color:red;margin-top:15px">{{ message }}</p>
      <button @click="saveTutorial" class="btn btn-success">Agregar</button>
      
    </div>

    <div v-else>
      <h4 style="text-align:center">Se agrego correctamente!</h4>
      <button class="btn btn-success" style="margin:auto;display:block" @click="newTutorial">Agregar otro tutorial</button>
    </div>
  
  </div>
</template>

<script>
import TutorialDataService from "../services/TutorialDataService";

export default {
  name: "add-tutorial",
  data() {
    return {
      tutorial: {
        id: null,
        title: "",
        description: "",
        published: false
      },
      submitted: false,
      message: ""
    };
  },
  methods: {
    saveTutorial() {
      var data = {
        title: this.tutorial.title,
        description: this.tutorial.description
      };

      TutorialDataService.create(data)
        .then(response => {
          this.tutorial.id = response.data.id;
          this.submitted = true;
          this.message = "";
        })
        .catch(error => {
          this.message = error.response.data.error;
        });
    },
    
    newTutorial() {
      this.submitted = false;
      this.tutorial = {};
    },

    goHome() {
        this.$router.push("/tutorials");
    }
  }
};
</script>

<style>
.submit-form {
  max-width: 300px;
  margin: auto;
}
</style>