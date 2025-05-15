<script>
export default {
  name: 'TodoItem',
  props: {
    todo: {
      type: Object,
    },
  },
  emits: ['update', 'delete'],
  data() {
    return {
      isEditing: false,
      newTitle: this.todo.title,
    }
  },
  methods: {
    toggle() {
      this.$emit('update', { ...this.todo, completed: !this.todo.completed })
    },

    changeTitle() {
      if (!this.isEditing) {
        return
      }

      this.isEditing = false

      if (this.newTitle === this.todo.title) {
        return
      }

      if (this.newTitle === '') {
        this.remove()
      } else {
        this.$emit('update', { ...this.todo, title: this.newTitle })
      }

      this.newTitle = ''
      this.isEditing = false
    },

    remove() {
      this.$emit('delete')
    },

    edit() {
      this.newTitle = this.todo.title
      this.isEditing = true

      this.$nextTick(() => {
        this.$refs['title-field'].focus()
      })
    },
  },
}
</script>

<template>
  <div class="todo" :class="{ completed: todo.completed }">
    <label class="todo__status-label">
      <input type="checkbox" class="todo__status" :checked="todo.completed" @change="toggle" />
    </label>

    <form v-if="isEditing" @submit.prevent="changeTitle">
      <input
        type="text"
        class="todo__title-field"
        placeholder="Empty todo will be deleted"
        v-model.trim="newTitle"
        ref="title-field"
        @keyup.esc="isEditing = false"
        @blur="changeTitle"
      />
    </form>

    <template v-else>
      <span class="todo__title" @dblclick="edit">{{ todo.title }}</span>

      <button class="todo__remove" @click="remove">x</button>
    </template>

    <div class="modal overlay" :class="{ 'is-active': false }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>
</template>
