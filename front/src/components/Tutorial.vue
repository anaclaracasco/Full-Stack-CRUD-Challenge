<template>
  <div v-if="currentTutorial" class="edit-form mw-100 w-50" style="max-width:300px;margin:auto">
    <div class="border border-dark shadow-lg p-3 mb-5 bg-white rounded col-md-12">
    <form>
      
      <div class="form-group">
        <h4>Editar Tutorial</h4>
        <hr>
        <label for="title" style="padding-bottom:10px">Titulo</label>
        <input
          type="text"
          class="form-control"
          id="title"
          v-model="currentTutorial.title"
        />
      </div>
      <div class="form-group">
        <label for="description" style="padding-bottom:10px;padding-top:10px">Descripción</label>
        <input
          type="text"
          class="form-control"
          id="description"
          v-model="currentTutorial.description"
        />
      </div>

      <div class="form-group">
        <label style="padding-top:10px"><strong>Estado:</strong></label>
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
          >Está seguro que desea eliminar el tutorial?</template
        >

        <template v-slot:footer> Será eliminado permanentemente!</template> />
      </Modal>

      <button type="submit" class="btn btn-success m-2" @click="updateTutorial">
        Actualizar
      </button>
      
    </div>
    
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
        this.message = "Título requerido";
      } else {
        TutorialDataService.update(
          this.currentTutorial.id,
          this.currentTutorial
        ).then(() => {
          this.message = "El tutorial se actualizó correctamente!";
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
  padding: 0.5rem 0.5rem;
}
</style>