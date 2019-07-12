<template>
  <div>
    <input type="text" class="todo-input" placeholder="Viec can lam" 
    v-model="newTodo" @keyup.enter="addTodo">
    <todo-item v-for="(todo, index) in todosFiltered" 
    :key="todo.id" :todo="todo" :index="index" :checkAll="!anyRemaining">
    </todo-item>

    <div class="extra-container">
      <todo-check-all></todo-check-all>
      <todo-items-remaining></todo-items-remaining>
    </div>

    <div class="extra-container">
      <todo-filtered></todo-filtered>
      
      <div>
        <transition name="fade">
        <todo-clear-completed></todo-clear-completed>
        </transition>
      </div>

    </div>
  </div>
</template>

<script>
import TodoItem from './TodoItem'
import TodoItemsRemaining from './TodoItemsRemaining'
import TodoCheckAll from './TodoCheckAll'
import TodoFiltered from './TodoFiltered'
import TodoClearCompleted from './TodoClearCompleted'

export default {
  name: 'todo-list',
  components: {
    TodoItem,
    TodoItemsRemaining,
    TodoCheckAll,
    TodoFiltered,
    TodoClearCompleted,
  },
  data () {
    return {
      newTodo: '', 
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': ';laksdjfasdkf',
          'completed': false,
          'editing': false,
        },
        {
          'id': 2,
          'title': ';laksdjffasdfasdfadsfasdkf',
          'completed': true,
          'editing': false,
        }
      ]
    }
  },
  computed: {
    remaining(){
      return this.$store.getters.remaining
    },
    anyRemaining(){
      return this.$store.getters.anyRemaining
    },
    todosFiltered(){
      return this.$store.getters.todosFiltered
    },
    showClearCompletedButton(){
      return this.$store.getters.showClearCompletedButton
    }
  },
  methods: {
    addTodo(){
      if(this.newTodo.trim() == ''){
        return
      }

      this.$store.commit('addTodo', {
        id: this.idForTodo,
        title: this.newTodo,
      })

      this.newTodo = ''
      this.idForTodo++
    }
  }
}
</script>

<style>
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
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
  }

  .remove-item:hover {
    color: black;
  }

  .todo-item-left {
    display: flex;
  }

  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }

  .todo-item-edit {
    font-size: 22px;
    color: aquamarine;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, sans-serif;
  }

  .todo-item-edit:focus {
    outline: none;
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
  }

  button:hover {
    background: lightgreen;
  }

  button:focus {
    outline: none;
  }

  .active {
    background: lightgreen;
  }

  .fade-enter-active, .fade-leave-active {
    transition:  opacity .2s;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
