<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="Что нужно сделать?"
      v-model="newToDo"
      @keyup.enter="addToDo"
    />
    <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
    <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
      <div class="todo-item-left">
        <input type="checkbox" v-model="todo.completed" />
        <div
          v-if="!todo.editing"
          @dblclick="editToDo(todo)"
          class="todo-item-label"
          :class="{ completed: todo.completed }"
        >
          {{ todo.title }}
        </div>
        <input
          v-else
          class="todo-item-edit"
          type="text"
          v-model="todo.title"
          @blur="doneEdit(todo)"
          @keyup.enter="doneEdit(todo)"
          @keyup.esc="cancelEdit(todo)"
          v-focus
        />
      </div>
      <div class="remove-item" @click="removeToDo(index)">
        &times;
      </div>
    </div>
    </transition-group>
    <div class="extra-container">
      <div>
        <label><input type="checkbox" :checked="!anyRemaining" @change="checkAllToDos"/>Выбрать всё</label>
      </div>
      <div>{{ remaining }} осталось</div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{ active: filter == 'all' }" @click="filter = 'all'">Все</button>
        <button :class="{ active: filter == 'active' }" @click="filter = 'active'">Активные</button>
        <button :class="{ active: filter == 'completed' }" @click="filter = 'completed'">Завершенные</button>
      </div>

      <div>
        <transition name="fade">
        <button v-if="showClearCompletedButton" @click="clearCompleted">Очистить завершенные</button>
        </transition>
      </div>

    </div>

  </div>
</template>

<script>
export default {
  name: "ToDoList",
  data() {
    return {
      newToDo: "",
      idForToDo: 3,
      beforeEditCache: "",
      filter: 'all',
      todos: [
        {
          id: 1,
          title: "Давай",
          completed: false,
          editing: false
        },
        {
          id: 2,
          title: "Работай",
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
    anyRemaining(){
      return this.remaining != 0
    },
    todosFiltered(){
      if(this.filter == 'all') {
        return this.todos
      }
      else if(this.filter == 'active') {
        return this.todos.filter(todo => !todo.completed)
      }
      else if(this.filter == 'completed') {
        return this.todos.filter(todo => todo.completed)
      }

      return this.todos
    },
    showClearCompletedButton() {
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  directives: {
    focus: {
      inserted: function(el) {
        el.focus();
      }
    }
  },
  methods: {
    addToDo() {
      if (this.newToDo.trim().length == 0) {
        return;
      }
      this.todos.push({
        id: this.idForToDo,
        title: this.newToDo,
        completed: false
      });

      this.newToDo = "";
      this.idForToDo++;
    },

    editToDo(todo) {
      this.beforeEditCache = todo.title;
      todo.editing = true;
    },

    doneEdit(todo) {
      if (todo.title.trim().length == 0) {
        todo.title = this.beforeEditCache;
      }
      todo.editing = false;
    },

    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.editing = false;
    },

    removeToDo(index) {
      this.todos.splice(index, 1);
    },

    checkAllToDos() {
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },

    clearCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    }
  }
};
</script>

<style lang="scss">
 @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.5.2/animate.min.css");


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
  animation-duration: 0.3s;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}

.todo-item-left {
  // later
  display: flex;
  align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 24px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc; //override defaults
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: grey;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
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
  // CSS Transitions
  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
