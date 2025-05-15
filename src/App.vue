<script>
import StatusFilter from './components/StatusFilter.vue'
import TodoItem from './components/TodoItem.vue'

import { getTodos, createTodo, updateTodo, deleteTodo } from './api/todos'

export default {
  components: {
    Filter: StatusFilter,
    TodoItem,
  },
  data() {
    return {
      todos: [],
      title: '',
      status: 'all',
    }
  },
  computed: {
    activeTodos() {
      return this.todos.filter((todo) => !todo.completed)
    },
    completedTodos() {
      return this.todos.filter((todo) => todo.completed)
    },
    visibleTodos() {
      if (this.status === 'all') {
        return this.todos
      } else if (this.status === 'active') {
        return this.activeTodos
      } else if (this.status === 'completed') {
        return this.completedTodos
      }
    },
  },
  // watch: {
  //   todos: {
  //     handler() {
  //       localStorage.setItem('todos', JSON.stringify(this.todos))
  //     },
  //     deep: true,
  //   },
  // },
  mounted() {
    getTodos()
      .then(({ data }) => {
        this.todos = data
      })
      .catch((error) => {
        console.error('Error fetching todos:', error)
      })
      .finally(() => {
        console.log('Todos fetched successfully')
      })
  },
  methods: {
    handleSubmit() {
      if (this.title.trim() === '') {
        return
      }

      createTodo(this.title)
        .then(({ data }) => {
          this.todos = [...this.todos, data]
          this.title = ''
        })
        .catch((error) => {
          console.error('Error creating todo:', error)
        })
        .finally(() => {
          console.log('Todo created successfully')
        })
    },

    updateTodo({ id, title, completed }) {
      updateTodo({ id, title, completed }).then(({ data }) => {
        this.todos = this.todos.map((todo) => {
          if (todo.id === id) {
            return { ...todo, title, completed }
          }
          return todo
        })
      })
    },

    deleteTodo(id) {
      deleteTodo(id).then(() => {
        this.todos = this.todos.filter((todo) => todo.id !== id)
      })
    },
  },
}
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button class="todoapp__toggle-all" :class="{ active: activeTodos.length === 0 }"></button>

        <form @submit.prevent="handleSubmit">
          <input
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <TransitionGroup name="list" tag="section" class="todoapp__main">
        <TodoItem
          v-for="(todo, index) of visibleTodos"
          :key="todo.id"
          :todo="todo"
          @update="updateTodo"
          @delete="deleteTodo(todo.id)"
        />
      </TransitionGroup>

      <footer class="todoapp__footer">
        <span class="todo-count"> {{ activeTodos.length }} items left </span>

        <Filter v-model="status" />

        <button class="todoapp__clear-completed" v-if="activeTodos.length > 0">
          Clear completed
        </button>
      </footer>
    </div>

    <article class="message is-danger message--hidden">
      <div class="message-header">
        <p>Error</p>
        <button class="delete"></button>
      </div>

      <div class="message-body">Unable to add a Todo</div>
    </article>
  </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
  max-height: 60px;
  transition: all 0.5s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  max-height: 0;
  transform: scaleY(0);
}
</style>
