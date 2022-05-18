<template>
  <div id="app-to-do">
    <h1>To-Do</h1>
    <label>Hide Done
      <input type="checkbox" v-model="hideDone"/>
    </label>
    <to-do-form @todo-added="addToDo"></to-do-form>
    <div class="stack-large">
      <draggable
        :list="ToDoItems"
        :disabled="!enabled"
        item-key="name"
        class="list-group"
        ghost-class="ghost"
      >
        <template #item="{ element }">
          <to-do-item
            v-show="!element.done || !hideDone"
            :note="element.note"
            :title="element.title" 
            :done="element.done" 
            :id="element.id"
            @checkbox-changed="updateDoneStatus(element.id)"
            @item-deleted="deleteToDo(element.id)"
            @item-clicked="editItem(element.id)">
          </to-do-item>
        </template>
      </draggable>
    </div>

    <!-- Modal from here -->
    <Teleport to="body" v-if="editedID">
      <!-- use the modal component, pass in the prop -->
      <to-do-item-edit-form
        :id="editedID"
        :title="editedTitle"
        :note="editedNote"
        v-if="showModal"
        @close="showModal = false"
        @item-edited="editToDo(editedID, $event)"
      >
      </to-do-item-edit-form>
    </Teleport>
  </div>
</template>

<script>
import uniqueId from 'lodash.uniqueid';
import draggable from 'vuedraggable';
import ToDoItem from './components/ToDoItem.vue';
import ToDoForm from './components/ToDoForm.vue';
import ToDoItemEditForm from './components/ToDoItemEditForm.vue';

const catNote = "![cat](https://github.com/deathvn/deathvn.github.io/blob/master/assets/blog/blog-thu-nghiem/cat.jpg?raw=true)_Cat image here_";

export default {
  name: 'App',
  components: {
    ToDoItem,
    ToDoForm,
    draggable,
    ToDoItemEditForm,
  },
  data() {
    return {
      editedID: "",
      showModal: false,
      hideDone: false,
      enabled: true,
      ToDoItems: [
        {id: uniqueId("todo-"), title: "Learn Vue", note: "## this is self-learn task", done: false},
        {id: uniqueId("todo-"), title: "Create a Vue project with the CLI", note: "project required this", done: true},
        {id: uniqueId("todo-"), title: "Have fun", note: catNote, done: true},
        {id: uniqueId("todo-"), title: "Create a to-do list", note: "should try it first", done: false},
      ]
    }
  },
  computed: {
    editedTitle() {
      const elToDo = this.ToDoItems.find(item => item.id === this.editedID);
      return elToDo.title;
    },
    editedNote() {
      const elToDo = this.ToDoItems.find(item => item.id === this.editedID);
      return elToDo.note;
    },
  },
  methods: {
    addToDo(toDoTitle) {
      this.ToDoItems.unshift({
        id: uniqueId("todo-"),
        title: toDoTitle,
        note: "",
        done: false,
      });
    },
    updateDoneStatus(toDoID) {
      const toDoToUpdate = this.ToDoItems.find(item => (item.id === toDoID));
      toDoToUpdate.done = !toDoToUpdate.done;
    },
    deleteToDo(toDoID) {
      const itemIndex = this.ToDoItems.findIndex(item => item.id === toDoID);
      this.ToDoItems.splice(itemIndex, 1);
    },
    editToDo(toDoID, newest) {
      const toToDoEdit = this.ToDoItems.find(item => item.id === toDoID);
      toToDoEdit.title = newest.title;
      toToDoEdit.note = newest.note;
    },
    editItem(toDoID) {
      this.editedID = toDoID;
      this.showModal = true;
    }
  }
}
</script>

<style>
/* Global styles */
.note {
  color: #585555;
  font-size: 1.4rem;
}
.note img {
  max-width: 100%;
}
.add-field {
  display: flex;
  justify-content: space-between;
}
.add-field > input {
  width: 100%;
}
.icon {
  width: 2rem;
  height: 2rem;
}
.btn {
  padding: 0.8rem 1rem 0.7rem;
  border: 0.2rem solid #4d4d4d;
  cursor: pointer;
  text-transform: capitalize;
}
.btn__success {
  background-color: #28a745;
}
.btn__danger {
  color: #fff;
  background-color: #ca3c3c;
  border-color: #bd2130;
}
.btn__filter {
  border-color: lightgrey;
}
.btn__danger:focus {
  outline-color: #c82333;
}
.btn__primary {
  color: #fff;
  background-color: #000;
}
.btn-group {
  display: flex;
  justify-content: space-between;
}
.btn-group > * {
  flex: 1 1 auto;
}
.btn-group > * + * {
  margin-left: 0.8rem;
}
.title-wrapper {
  margin: 0;
  flex: 0 0 100%;
  text-align: center;
}
[class*="__lg"] {
  display: inline-block;
  width: 100%;
  font-size: 1.9rem;
}
[class*="__lg"]:not(:last-child) {
  margin-bottom: 1rem;
}
@media screen and (min-width: 620px) {
  [class*="__lg"] {
    font-size: 2.4rem;
  }
}
.visually-hidden {
  position: absolute;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: rect(1px, 1px, 1px, 1px);
  white-space: nowrap;
}
[class*="stack"] > * {
  margin-top: 0;
  margin-bottom: 0;
}
.stack-small {
  border: 0.1rem solid lightgrey;
}
.stack-small > * + * {
  margin-top: 1.25rem;
}
.stack-large {
  margin-top: 2.5rem;
}
.stack-large > .list-group > * + * {
  margin-top: 1rem;
}
@media screen and (min-width: 550px) {
  .stack-small > * + * {
    margin-top: 1.4rem;
  }
  .stack-large > * + * {
    margin-top: 2.8rem;
  }
}
/* End global styles */
#app {
  background: #fff;
  margin: 2rem 0 4rem 0;
  padding: 1rem;
  padding-top: 0;
  position: relative;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 2.5rem 5rem 0 rgba(0, 0, 0, 0.1);
}
@media screen and (min-width: 550px) {
  #app {
    padding: 4rem;
  }
}
#app > * {
  max-width: 50rem;
  margin-left: auto;
  margin-right: auto;
}
#app > form {
  max-width: 100%;
}
#app-to-do > h1 {
  display: block;
  min-width: 100%;
  width: 100%;
  text-align: center;
  margin: 0;
  margin-bottom: 1rem;
}
</style>
