<template>
  <form @submit.prevent="onSubmit">
    <h2 class="title-wrapper">
      <label for="new-todo-input" class="title__lg">
        What needs to be done?
      </label>
    </h2>
    <div class="add-field">
      <input
        type="text"
        id="new-todo-input"
        name="new-todo"
        autocomplete="off"
        v-model.trim="title"
      />
      <button class="btn btn__primary" type="submit">+</button>
    </div>
  </form>
</template>

<script>
export default {
  methods: {
    onSubmit() {
      if (this.title==='') {
        return;
      }
      this.$emit("todo-added", this.title);
      this.title="";
    }
  },

  data() {
    return {
      title: ""
    }
  },
}
</script>