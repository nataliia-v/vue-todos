<script>
import StatusFilter from './components/StatusFilter.vue'
import TodoItem from './components/TodoItem.vue'

export default {
  components: {
    Filter: StatusFilter,
    TodoItem,
  },
  data() {
    let todos = []
    const jsonData = localStorage.getItem('todos') || '[]'

    try {
      todos = JSON.parse(jsonData)
    } catch (e) {}

    return {
      todos,
      title: '',
      status: 'all',
    }
  },
  computed: {
    activeTodos() {
      return this.todos.filter((todo) => !todo.completed)
    },
  },
  watch: {
    todos: {
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos))
      },
      deep: true,
    },
  },
  methods: {
    handleSubmit() {
      if (this.title.trim() === '') {
        return
      }
      this.todos.push({
        id: Date.now(),
        title: this.title,
        completed: false,
      })
      this.title = ''
    },
  },
}
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos.length</h1>

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

      <section class="todoapp__main">
        <TodoItem
          v-for="(todo, index) of todos"
          :key="todo.id"
          :todo="todo"
          @update="todos[index] = $event"
          @delete="todos.splice(index, 1)"
        />
      </section>

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
