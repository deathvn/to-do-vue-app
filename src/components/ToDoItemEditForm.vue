<template>
  <Transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header"><h2>Edit Task</h2></slot>
          </div>

          <div class="modal-body">
            <div class="form-row">
              <label for="task-title">Title</label>
              <input
                id="task-title"
                type="text"
                autocomplete="off"
                v-model.trim="newTitle"
              />
            </div>
            <div class="form-row">
              <label for="task-note">Note</label>
              <textarea 
                id="task-note"
                v-model.trim="newNote"
                placeholder="Add note for your task (Markdown supported)">
              </textarea>
            </div>
          </div>

          <div class="modal-footer">
            <slot name="footer">
              <div class="btn-group">
                <button type="button" class="btn btn__primary" @click="onCancel">
                  <!-- <img class="icon" src="../assets/xmark-solid.svg"> -->
                  Cancel
                  <!-- <span class="visually-hidden">editting {{title}}</span> -->
                </button>
                <button type="submit" class="btn" :class="{'btn__success': isChanged}" @click="onSubmit" :disabled='!isChanged'>
                  <!-- <img class="icon" src="../assets/check-solid.svg"> -->
                  Save
                  <!-- <span class="visually-hidden">edit for {{title}}</span> -->
                </button>
              </div>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script>
export default {
  props: {
    note: {
      type: String,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    id: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      newTitle: this.title,
      newNote: this.note,
    };
  },
  computed: {
    isChanged() {
      const isChangeTitle = this.newTitle !== this.title;
      const isChangeNote = this.newNote !== this.note;
      // Task title must not be blank
      return this.newTitle && (isChangeTitle || isChangeNote);
    }
  },
  methods: {
    onSubmit() {
      if (this.isChanged) {
        this.$emit("item-edited", {
          title: this.newTitle,
          note: this.newNote
        });
      }
      this.$emit("close");
    },
    onCancel() {
      this.$emit("close");
    }
  },
}
</script>

<style scoped>
.form-row {
  padding-top: 20px;
}
.edit-title {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #0b0c0c;
  display: block;
  margin-bottom: 5px;
}
input {
  display: inline-block;
  width: 100%;
  min-height: 4.4rem;
  padding: 0.4rem 0.8rem;
  border: 2px solid #565656;
}
textarea {
  width: 100%;
  min-height: 20rem;
  resize:vertical;
}
form {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
form > * {
  flex: 0 0 100%;
}

/* Modal Style */
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  max-width: 852px;
  height: auto;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
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

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>