<template>
 <div>
     <input type="text" class="todo-input" placeholder="What needs to be done..." v-model="newTodo" @keyup.enter="addTodo">
     <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
     <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
         <div class="todo-item-left">
             <input type="checkbox" v-model="todo.completed">
             <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class = "{completed : todo.completed}">
                {{ todo.title }}
             </div>
            <input v-else type="text" v-model="todo.title" class="todo-item-edit" @blur="doneEditing(todo)" @keyup.enter="doneEditing(todo)" @keyup.esc="cancelEditing(todo)" v-focus>
         </div>
         <div class="remove-item" @click="removeTodo(index)">
             &times;
         </div>
     </div>
     </transition-group>


     <div class="extra-container">
         <div><label><input type="checkbox" :checked ="!anyRemaining" @change ="checkAllTodos"> Check All</label></div>
         <div>{{ remaining }} items left</div>
     </div>
     <div class="extra-container">
         <div>
             <button :class="{ active: filter == 'all'}" @click="filter = 'all'">All</button>
             <button :class="{ active: filter == 'active'}" @click="filter = 'active'">
                 Active</button>
             <button :class="{ active: filter == 'completed'}" @click="filter = 'completed'">Completed</button>
         </div>

         <div>
             <transition name="fade">
                 <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
             </transition>
         </div>

     </div>
 </div>
</template>

<script>
export default { 
  name: 'Todolist',
  props: {
    
  },

  data() {
      return {
    newTodo: '',
    idForTodo: 6,
    beforeEditingcache: '',
    filter: 'all',
    todos: [
        {
            'id': 1,
            'title': 'Finish vue course by Andre',
            'completed': false,
            'editing': false
        },

        {
            'id': 2,
            'title': 'Take over the world',
            'completed': false,
            'editing': false
        },

        
        {
            'id': 3,
            'title': 'Finish all Courses for this year',
            'completed': false,
            'editing': false
        },

         {
            'id': 4,
            'title': 'Start all Courses for this year',
            'completed': false,
            'editing': false
        },

        {
            'id': 5,
            'title': 'Start all Courses for next year',
            'completed': false,
            'editing': false
        },
    ]
      }
  },

  directives: {
    focus: {
        inserted: function(el) {
            el.focus()
        }
    }  
  },

  computed: {
      remaining () {
          return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining() {
          return this.remaining != 0
      },
      todosFiltered () {
        if (this.filter == 'all')  {
            return this.todos
        }else if (this.filter =="active") {
            return this.todos.filter(todo => !todo.completed)
        }else if (this.filter =="completed") {
            return this.todos.filter(todo => todo.completed)            
        }
        return this.todos
      },

      showClearCompletedButton (){
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },

  methods: {
      addTodo () {
          if (this.newTodo.trim().length == 0) {
              return;
          }
        this.todos.push({
            id:this.idForTodo, 
            title:this.newTodo,
            completed: false,
            editing: false
        })
        this.newTodo =''
        this.idForTodo++
      },
      removeTodo(index){
         this.todos.splice(index, 1)
      },

      editTodo (todo) {
        this.beforeEditingcache = todo.title
        todo.editing = true  
      },

      doneEditing (todo) {
           if (todo.title.trim().length == 0) {
               todo.title = this.beforeEditingcache
          }
       todo.editing = false;   
      },

      cancelEditing (todo){
        todo.title = this.beforeEditingcache;
        todo.editing = false
      },
      checkAllTodos (){
        this.todos.forEach((todo) => todo.completed = event.target.checked)  
      },
      clearCompleted () {
       this.todos = this.todos.filter(todo => !todo.completed)   
      }
  } 
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
    .todo-input {
        width: 100%;
        padding: 10px 18px;
        font-size: 18px;
        margin-bottom: 16px;

        &:focus{
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
        color : gray;
        &:hover {
            color:red;
        }
    }

    .todo-item-left {
        display: flex;
        align-items: center;
    }

    .todo-item-label{
        padding: 10px;
        border: 1px solid white;
        margin-left: 12px;
    }

    .todo-item-edit {
        font-size: 24px;
        color: #2c3e50;
        margin-left: 12px;
        width: 400px;
        padding: 10px;
        border: 1px solid #ccc;
        font-family:'Avenir', Arial, Helvetica, sans-serif;
        //display: none;
        &:focus {
            outline : none;
        }
    }

    .completed {
        text-decoration: line-through;
        color: red; 
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
        .fade-enter-active, .fade-leave-active {
            transition: opacity .2s;
        }

        .fade-enter, .fade-leave-to {
            opacity: 0;
        }
</style> 