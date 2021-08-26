<template>
  <transition name="modal-fade">
    <div class="modal-backdrop">
      <div class="modal">
        <header class="modal-header">
          <slot name="header"> Default </slot>
          <button type="button" class="btn-close" @click="close">x</button>
        </header>

        <section class="modal-body">
          <slot name="body"> Default </slot>
        </section>

        <footer class="modal-footer">
          <slot name="footer"> Default </slot>
          <div class="row">
            <div class="col-12 col-sm-6 col-md-6">
              <button type="button" class="btn btn-danger" @click="doDelete">
                Si
              </button>
            </div>
            <div class="col-12 col-sm-6 col-md-6">
              <button type="button" class="btn btn-success" @click="close">
                No
              </button>
            </div>
          </div>
        </footer>
      </div>
    </div>
  </transition>
</template>

<script>
import TutorialDataService from "../services/TutorialDataService";

export default {
  name: "Modal",
  props: {
    currentTutorialId: { type: Number },
  },
  methods: {
    close() {
      this.$emit("close");
    },
    doDelete() {
      if (this.currentTutorialId == null) {
        TutorialDataService.deleteAll().then(() => {
          this.$emit("close");
        });
      } else {
        TutorialDataService.delete(this.currentTutorialId).then(() => {
          this.$router.push({ name: "tutorials" });
        });
      }
    },
  },
};
</script>

<style>
.modal-backdrop {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(0, 0, 0, 0.3);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal {
  background: #ffffff;
  box-shadow: 2px 2px 20px 1px;
  overflow-x: auto;
  display: flex;
  flex-direction: column;
  width: 25%;
  height: 30%;
  position: relative;
  overflow: hidden;
}

.modal-header,
.modal-footer {
  padding: 15px;
  display: flex;
}

.modal-header {
  position: relative;
  border-bottom: 1px solid #eeeeee;
  color: #f00813;
  justify-content: center;
}

.modal-footer {
  border-top: 1px solid #eeeeee;
  flex-direction: column;
  justify-content: flex-end;
}

.modal-body {
  position: relative;
  padding: 20px 10px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-fade-enter,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.5s ease;
}

.btn-close {
  position: absolute;
  top: 0;
  right: 0;
  border: none;
  font-size: 20px;
  padding: 10px;
  cursor: pointer;
  font-weight: bold;
  color: #4aae9b;
  background: transparent;
}

.btn-green {
  color: white;
  background: #4aae9b;
  border: 1px solid #4aae9b;
  border-radius: 2px;
}
</style>