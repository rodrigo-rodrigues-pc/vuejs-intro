<script>
import TodoItem from "./components/TodoItem.vue";

export default {
  components: {
    TodoItem
  },

  data() {
    return {
      items: [],
      newItemTitle: '',
      hideCompleted: false,
    };
  },

  computed: {
    itemsToDisplay() {
      if (!this.hideCompleted) {
        return this.items;
      }

      return this.items.filter(item => !item.completed);
    }
  },

  methods: {
    add() {
      const item = {
        _id: new Date().toISOString(),
        title: this.newItemTitle,
        completed: false
      };

      this.items.push(item);

      this.newItemTitle = '';
      this.$refs.titleInput.focus();
    },

    remove(item) {
      const index = this.items.indexOf(item);
      this.items.splice(index, 1);
    },

    toggleCompleted(item) {
      item.completed = !item.completed;
    }
  },
}
</script>

<template>
  <div style="width: 600px;">
    <div id="myDIV" class="header">
      <h2>My To-Do List</h2>
      <input type="text" placeholder="Title..." v-model="newItemTitle" ref="titleInput" @keyup.enter="add">
      <span @click="add" class="addBtn">Add</span>
      <label class="hide-completed">
        <input type="checkbox" v-model="hideCompleted" />
        Hide completed items
      </label>
    </div>

    <h2 v-if="!items.length">No item in your todo list.</h2>
    <ul id="myUL">
      <todo-item v-for="item in itemsToDisplay" :key="item.id"
                 :title="item.title" :completed="item.completed"
                 @remove="remove(item)" @toggleCompleted="toggleCompleted(item)"
      />
    </ul>
  </div>
</template>

<style scoped>

</style>
