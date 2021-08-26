<template>
  <div v-if="currentTutorial" class="edit-form  ">
    <h4>Tutorial</h4>
    <form>
      <div class="form-group">
        <label for="title">Titulo</label>
        <input
          type="text"
          class="form-control"
          id="title"
          v-model="currentTutorial.title"
        />
      </div>
      <div class="form-group">
        <label for="description">Descripcion</label>
        <input
          type="text"
          class="form-control"
          id="description"
          v-model="currentTutorial.description"
        />
      </div>

      <div class="form-group">
        <label><strong>Estado:</strong></label>
        {{ currentTutorial.published ? "Publicado" : "Pendiente" }}
        <p style="color:red;margin-top:15px">{{ message }}</p>
      </div>
    </form>
    <div style="margin: auto; display: block;">
      <button
        class="btn btn-secondary m-2" 
        v-if="currentTutorial.published"
        @click="updatePublished(false)"
      >
        Pendiente
      </button>
      <button
        v-else
        class="btn btn-primary m-2"
        @click="updatePublished(true)"
      >
        Publicar
      </button>
      <button type="button" class="btn btn-danger m-2" @click="showModal">
        Eliminar
      </button>

      <Modal
        v-show="isModalVisible"
        :currentTutorialId="currentTutorial.id"
        @close="closeModal"
      >
        <template v-slot:header> Eliminar </template>

        <template v-slot:body
          >Esta seguro que desea eliminar el tutorial?</template
        >

        <template v-slot:footer> Sera eliminado permanentemente!</template> />
      </Modal>

      <button type="submit" class="btn btn-success m-2" @click="updateTutorial">
        Actualizar
      </button>
    </div>
  </div>
</template>

<script>
import TutorialDataService from "../services/TutorialDataService";
import Modal from "./Modal";

export default {
  components: {
    Modal,
  },
  name: "tutorial",
  data() {
    return {
      currentTutorial: null,
      message: "",
      isModalVisible: false,
    };
  },

  methods: {
    getTutorial(id) {
      TutorialDataService.get(id).then((response) => {
        this.currentTutorial = response.data;
      });
    },

    updatePublished(status) {
      var data = {
        id: this.currentTutorial.id,
        title: this.currentTutorial.title,
        description: this.currentTutorial.description,
        published: status,
      };

      TutorialDataService.update(this.currentTutorial.id, data).then(() => {
        this.currentTutorial.published = status;
        this.message = "";
      });
    },

    updateTutorial() {
      if (!this.currentTutorial.title) {
        this.message = "Titulo requerido";
      } else {
        TutorialDataService.update(
          this.currentTutorial.id,
          this.currentTutorial
        ).then(() => {
          this.message = "El tutorial se actualizo correctamente!";
        });
      }
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
    },
  },
  mounted() {
    this.message = "";
    this.getTutorial(this.$route.params.id);
  },
};
</script>

<style>
.edit-form {
  max-width: 300px;
  margin: auto;
}
.btn {
  padding:.500rem .500rem;
}
</style>