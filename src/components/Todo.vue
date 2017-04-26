<template>
<section class="todo-app">
  <header class="header">
    <h1>Todo</h1>
    <input type="text" class="new-todo" placeholder="Ajouter une tache" v-model="newtodo" @keyup.enter="addTodo">
  </header>
  <div class="main">
    <input type="checkbox" class="toggle-all" v-model="allDone">
    <ul class="todo-list">
      <li class="todo" v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: todo === editing}">
        <div class="view">
          <input type="checkbox" v-model="todo.completed" class="toggle">
          <label @dblclick="editTodo(todo)">{{ todo.name }}</label>
          <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
        </div>
        <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit" @keyup.esc="cancelEdit" @blur="doneEdit" v-focus="todo === editing">
      </li>
    </ul>
  </div>
  <footer class="footer" v-show="todos.length > 0">
    <span class="todo-count"><strong>{{ remaining }}</strong> tâches à faire</span>
    <ul class="filters">
      <li><a href="#" :class="{selected: filter === 'all'}" @click.prevent="filter = 'all'">Toutes</a></li>
      <li><a href="#" :class="{selected: filter === 'todo'}" @click.prevent="filter = 'todo'">À faire</a></li>
      <li><a href="#" :class="{selected: filter === 'done'}" @click.prevent="filter = 'done'">Faites</a></li>
    </ul>
    <button class="clear-completed" @click.prevent="deleteCompleted()" v-show="completed">Supprimer les tâches finies</button>
  </footer>
</section>
</template>

<script>
import Vue from 'vue'

export default {
  data() {
    return {
      todos: [{
        name: 'Tache de test',
        completed: false
      }],
      newtodo: '',
      filter: 'all',
      editing: null,
      oldTodo: ''
    }
  },
  methods: {
    addTodo() {
      this.todos.push({
        completed: false,
        name: this.newtodo
      })
      this.newtodo = ''
    },
    deleteTodo(todo) {
      this.todos = this.todos.filter(i => i !== todo)
    },
    deleteCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    editTodo(todo) {
      this.editing = todo
      this.oldTodo = todo.name
    },
    doneEdit() {
      this.editing = null
    },
    cancelEdit() {
      this.editing.name = this.oldTodo
      this.doneEdit()
    }
  },
  computed: {
    allDone: {
      get() {
        return this.remaining === 0
      },
      set(value) {
        this.todos.forEach(todo => {
          todo.completed = value
        })
      }
    },
    remaining() {
      return this.todos.filter(todo => !todo.completed).length
    },
    completed() {
      return this.todos.filter(todo => todo.completed).length
    },
    filteredTodos() {
      if (this.filter === 'todo') {
        return this.todos.filter(todo => !todo.completed)
      } else if (this.filter === 'done') {
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    }
  },
  directives: {
    focus(el, value) {
      if (value) {
        Vue.nextTick(_ => {
          el.focus()
        })
      }
    }
  }
}
</script>

<style src="./todo.css"></style>
