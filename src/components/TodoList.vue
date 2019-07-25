<template>
  <div>
    <input
      type="text"
      class="todo-input"
      v-model="newTodo"
      placeholder="What needs to be done?"
      @keyup.enter="addTodo"
    />
    <div
      v-for="(todo, index) in filteredTodos"
      class="todo-item"
      :key="todo.id"
    >
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" />
        <div
          class="todo-item-label"
          :class="{ completed: todo.completed }"
          v-if="!todo.editing"
          @dblclick="editTodo(todo)"
        >
          {{ todo.title }}
        </div>
        <input
          v-else
          class="todo-item-edit"
          type="text"
          v-model="todo.title"
          @blur="doneEditing(todo)"
          @keyup.enter="doneEditing(todo)"
          @keyup.esc="cacelEdit(todo)"
          v-focus
        />
      </div>
      <div class="remove-item" @click="removeTodo(index)">&times;</div>
    </div>

    <div class="extra-container">
      <div>
        <label>
          <input
            type="checkbox"
            :checked="remaining === 0 && todos.length !== 0"
            @change="toggleCheckAll"
            :disabled="remaining === 0 || todos.length === 0"
          />
          Check All
        </label>
      </div>
      <div>{{ remaining }} items Left</div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">
          All
        </button>
        <button
          :class="{ active: filter == 'active' }"
          @click="filter = 'active'"
        >
          Active
        </button>
        <button
          :class="{ active: filter == 'completed' }"
          @click="filter = 'completed'"
        >
          Completed
        </button>
      </div>
      <div>
        <transition name="fade">
          <button @click="removeCompleted" v-show="showClearCompleted">
            Clear Completed Todos
          </button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-list",
  props: {},
  //creating custom directives
  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },
  data() {
    return {
      newTodo: "",
      beforeEditCache: "",
      nextId: 3,
      filter: "all",
      todos: [
        { id: 1, title: "Finish Todo App", completed: false, editing: false },
        {
          id: 2,
          title: "Convince Ibra to use Vue",
          completed: false,
          editing: false
        }
      ]
    };
  },
  computed: {
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    showClearCompleted() {
      return this.todos.filter(todo => todo.completed).length > 0;
    },
    filteredTodos() {
      let filteredTodos = [];
      switch (this.filter) {
        case "all":
          filteredTodos = this.todos;
          break;
        case "active":
          filteredTodos = this.todos.filter(todo => !todo.completed);
          break;
        case "completed":
          filteredTodos = this.todos.filter(todo => todo.completed);
          break;
        default:
          filteredTodos = this.todos;
          break;
      }
      return filteredTodos;
    }
  },
  methods: {
    // Add a new Todo
    addTodo() {
      if (this.newTodo.trim().length === 0) {
        return;
      }
      this.todos.push({
        id: this.nextId,
        title: this.newTodo,
        completed: false,
        editing: false
      });
      this.newTodo = "";
      this.nextId += 1;
    },
    // Edit a todo
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },
    //Cacel the edit
    cacelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },
    doneEditing(todo) {
      if (todo.title.trim().length === 0) {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },
    // Remove a todo
    removeTodo(index) {
      this.todos.splice(index, 1);
    },

    toggleCheckAll() {
      this.todos.forEach(todo => {
        todo.completed = event.target.checked;
      });
    },

    removeCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;

  &:focus {
    outline: 0;
  }
}
.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: red;
  }
}

.todo-item-left {
  display: flex;
  align-items: center;
}

.todo-item-label {
  padding: 18px;
  border: 1px solid white;
  margin-left: 12px;
}

.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 18px;
  border: 1px solid #ccc;
  font-family: "Avenir", Arial, Helvetica, sans-serif;

  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgray;
  padding-top: 14px;
  margin-bottom: 14px;
}

button {
  font-size: 14px;
  background-color: white;
  appearance: none;

  &:hover {
    background: lightgreen;
  }

  &:focus {
    outline: none;
  }
}
.active {
  background: lightgreen;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
