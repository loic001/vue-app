<template>
    <section class="todoapp">
        <header class="header">
            <h1>Todos</h1>
            <input type="text" class="new-todo" placeholder="Ajouter une tache" v-model="newTodo" @keyup.enter="addTodo">
        </header>
        <div class="main">
            <input type="checkbox" class="toggle-all" v-model="allDone" >
            <label for="toggle-all"></label>
            <ul class="todo-list">
                <li class="todo" v-for="todo in filteredTodos" :class="{completed: todo.completed, editing: todo === editing}">
                    <div class="view">
                        <input type="checkbox" v-model="todo.completed" class="toggle">
                        <label @dblclick="editTodo(todo)"> {{ todo.name }} </label>
                        <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
                    </div>
                    <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit" @blur="doneEdit" @keyup.esc="cancelEdit" v-focus="todo === editing">
                </li>
            </ul>
        </div>
        <footer class="footer" v-show="hasTodo">
            <span class="todo-count" ><strong>{{ remaining }} </strong> tache à faire</span>
            <ul class="filters">
                <li> <a href="#" :class="{selected: filter === 'all'}" @click.prevent="filter = 'all'">Toute</a> </li>
                <li> <a href="#" :class="{selected: filter === 'todo'}" @click.prevent="filter = 'todo'">A faire</a> </li>
                <li> <a href="#" :class="{selected: filter === 'done'}" @click.prevent="filter = 'done'">Faite</a> </li>
            </ul>
            <button class="clear-completed" v-show="completed" @click.prevent="deleteCompleted">supprimer les tache</button>
        </footer>
    </section>
</template>


<script>
import Vue from 'vue'
export default {
    props: {
        value: {type: Array, default () { return [] }}
    },
    data () {
        return {           
            todos: this.value ,
            newTodo: '',
            filter: 'all',
            editing: null,
            oldTodo: ''
           }
    },
        watch: {
            value (value) {
                this.todos = value
            }
        },
    
    
    methods: {
        addTodo () {
           this.todos.push({
               completed: false,
               name: this.newTodo
           }) 
           this.newTodo = ''
            
        },
        deleteTodo (todo) {
            this.todos = this.todos.filter(i => i  !== todo )
            this.$emit('input', this.todos)
         },
    
        deleteCompleted () {
            this.todos = this.todos.filter(todo => !todo.completed)
            this.$emit('input', this.todos)
               },
        editTodo (todo) {
            this.editing = todo
            this.oldTodo = todo.name
        },
        doneEdit () {
            this.editing = null
        },
        cancelEdit (){
            this.editing.name = this.oldTodo
            this.doneEdit()
            }
        },
       
    computed: {

        allDone: {
            get () {
                return this.remaining === 0
            },
            set (value) {
                  this.todos.forEach(todo => {
                      todo.completed = value
                  })
              }
        },
        remaining () {
           return  this.todos.filter(todo => !todo.completed).length
        },
        completed () {
            return this.todos.filter(todo => todo.completed).length
        },
        hasTodo () {
            return this.todos.length > 0
        },
        filteredTodos () {
            if (this.filter === 'todo') { 
               return this.todos.filter(todo => !todo.completed)
          } else if (this.filter === 'done') {
               return this.todos.filter(todo => todo.completed)
            }
            return this.todos
        } 
    },
    directive: {
        focus (el, value){
            if (value) {
                Vue.nextTick(_ => { 
                  el.focus()
                })
            }
        }
    }
}
</script>

<style src="./todos.css"></style>