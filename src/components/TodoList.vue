<template>
  <div>
    <input type="text" class="todo-input" placeholder="Viec can lam" 
    v-model="newTodo" @keyup.enter="addTodo">
    <todo-item v-for="(todo, index) in todosFiltered" 
    :key="todo.id" :todo="todo" :index="index" :checkAll="!anyRemaining">
    </todo-item>

    <div class="extra-container">
      <todo-check-all :anyRemaining="anyRemaining"></todo-check-all>
      <todo-items-remaining :remaining="remaining"></todo-items-remaining>
    </div>

    <div class="extra-container">
      <todo-filtered></todo-filtered>
      
      <div>
        <transition name="fade">
        <button v-if="showClearCompletedButton" @click="clearCompleted">Clear completed</button>
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

export default {
  name: 'todo-list',
  components: {
    TodoItem,
    TodoItemsRemaining,
    TodoCheckAll,
    TodoFiltered,
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
  created(){
    eventBus.$on('removedTodo', (index) => this.removeTodo(index))
    eventBus.$on('finishedEdit', (data) => this.finishedEdit(data))
    eventBus.$on('checkAllChanged', (checked) => this.checkAllTodos(checked))
    eventBus.$on('filterChanged', (filter) => this.filter = filter)
  },
  computed: {
    remaining(){
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining(){
      return this.remaining != 0
    },
    todosFiltered(){
      if(this.filter == 'all'){
        return this.todos
      } else if (this.filter == 'active'){
        return this.todos.filter(todo => !todo.completed)
      } else if (this.filter == 'completed'){
        return this.todos.filter(todo => todo.completed)
      }
    },
    showClearCompletedButton(){
      return this.todos.filter(todo => todo.completed).length > 0
    }
  },
  methods: {
    addTodo(){
      if(this.newTodo.trim() == ''){
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false,
      })

      this.newTodo = ''
      this.idForTodo++
    },
    removeTodo(index){
      this.todos.splice(index, 1)
    },
    checkAllTodos(){
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },
    clearCompleted(){
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    finishedEdit(data){
      this.todos.splice(data.index, 1, data.todo)
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
