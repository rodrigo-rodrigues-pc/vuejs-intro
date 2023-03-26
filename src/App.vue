<script>
import TodoItem from "./components/TodoItem.vue";

const db = new PouchDB('todo_app');

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

  mounted() {
    this.fetchItems();

    db.changes({
      since: 'now',
      live: true
    }).on('change', this.fetchItems);
  },

  computed: {
    itemsToDisplay() {
      if (!this.hideCompleted) {
        return this.items;
      }

      return this.items.filter(item => !item.doc.completed);
    }
  },

  methods: {
    fetchItems() {
      db.allDocs({include_docs: true, descending: true}, (err, doc) => {
        this.items = doc.rows;
      });
    },

    add() {
      const item = {
        _id: new Date().toISOString(),
        title: this.newItemTitle,
        completed: false
      };

      db.put(item, () => {
        this.newItemTitle = '';
        this.$refs.titleInput.focus();
      });
    },

    remove(item) {
      db.remove(item);
    },

    toggleCompleted(item) {
      item.completed = !item.completed;
      db.put(item);
    }
  },
}
</script>

<template>
  <div style="width: 600px;">
    <div id="myDIV" class="header">
      <h2>
        <i class="fa fa-thin fa-list fa-fw" style="color: #007483;"></i> My To-Do List
      </h2>
      <input type="text" placeholder="Title..." v-model="newItemTitle" ref="titleInput" @keyup.enter="add">
      <span @click="add" class="addBtn">Add</span>
      <label class="switch hide-completed">
        <input type="checkbox" v-model="hideCompleted" id="hide-completed-checkbox">
        <span class="slider round"></span>
      </label>
      <label class="hide-completed hide-completed-label" for="hide-completed-checkbox" style="cursor: pointer">
        Hide completed items
      </label>
    </div>

    <h2 v-if="!itemsToDisplay.length">No items to display.</h2>
    <ul id="myUL">
      <todo-item v-for="item in itemsToDisplay" :key="item.doc.id"
                 :title="item.doc.title" :completed="item.doc.completed"
                 @remove="remove(item.doc)" @toggleCompleted="toggleCompleted(item.doc)"
      />
    </ul>
  </div>
</template>

<style scoped>

</style>
