<template>
  <div class="list row">
    <div class="col-md-12">
      <div class="input-group mb-4">
        <input
          type="text"
          class="form-control"
          placeholder="Buscar por titulo"
          v-model="title"
        />

        <div class="input-group-append">
          <button
            class="btn btn-outline-primary"
            type="button"
            @click="searchTitle"
          >
            Buscar
          </button>
        </div>
      </div>
    </div>
    <div class="col-md-6">
      <h4>Lista de Tutoriales</h4>
      <hr />
      <p v-if="!tutorials">No se encontraron tutoriales!</p>
      <p v-else-if="isEmpty">No hay tutoriales</p>
      <ul class="list-group">
        <li
          class="list-group-item"
          :class="{ active: index == currentIndex }"
          v-for="(tutorial, index) in tutorials"
          :key="index"
          @click="setActiveTutorial(tutorial, index)"
        >
          {{ tutorial.title }}
        </li>
      </ul>
      <br />

      <Modal v-show="isModalVisible" @close="closeModal">
        <template v-slot:header> Eliminar Todos </template>

        <template v-slot:body
          >Esta seguro que quiere eliminar todos los tutoriales?</template
        >

        <template v-slot:footer> Seran eliminados permanentemente!</template> />
      </Modal>
      <button
        v-if="!search && !isEmpty"
        class="btn btn-sm btn-danger float-right"
        @click="showModal"
      >
        Eliminar Todos
      </button>
      <button v-else-if="search"
      class="btn btn-sm btn-danger"
        @click="retrieveTutorials"
      >
        Limpiar busqueda
      </button>
    </div>
    <div class="col-md-6">
      <h4>Tutorial</h4>
      <hr />
      <div v-if="currentTutorial">
        <div>
          <label><strong>Titulo:</strong></label> {{ currentTutorial.title }}
        </div>
        <div>
          <label><strong>Descripcion:</strong></label>
          {{ currentTutorial.description }}
        </div>
        <div>
          <label><strong>Estado:</strong></label>
          {{ currentTutorial.published ? "Publicado" : "Pendiente" }}
        </div>
        <router-link
          :to="`/tutorials/${currentTutorial.id}`"
          class="btn btn-warning"
          >Editar</router-link
        >
      </div>
      <div v-else>
        <p>Haz click en un tutorial...</p>
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
  name: "tutorials-list",
  data() {
    return {
      tutorials: [],
      currentTutorial: null,
      currentIndex: -1,
      title: "",
      search: undefined,
      isEmpty: undefined,
      isModalVisible: false,
    };
  },
  methods: {
    retrieveTutorials() {
      TutorialDataService.getAll().then((response) => {
        if (response.data.length == 0) {
          this.isEmpty = true;
        }
        this.tutorials = response.data;
        this.title = "";
        this.search = false;
        this.currentTutorial = null;
        this.currentIndex = -1;
      });
    },

    refreshList() {
      this.retrieveTutorials();
      this.currentTutorial = null;
      this.currentIndex = -1;
    },

    setActiveTutorial(tutorial, index) {
      this.currentTutorial = tutorial;
      this.currentIndex = index;
    },
    searchTitle() {
      TutorialDataService.findByTitle(this.title).then((response) => {
        this.currentTutorial = null;
        this.search = true;
        this.currentIndex = -1;
        if (response.data.length == 0) {
          this.tutorials = null;
        } else {
          this.tutorials = response.data;
        }
      });
    },
    showModal() {
      this.isModalVisible = true;
    },
    closeModal() {
      this.isModalVisible = false;
      this.refreshList();
    },
  },
  mounted() {
    this.retrieveTutorials();
  },
};
</script>

<style>
.list {
  text-align: left;
  max-width: 750px;
  margin: auto;
}
</style>