<template>
    <div class="card">
        <div class="card-body mb-6">
            <h1 class="text-grey-darkest">Todo List</h1>
            <div class="flex mt-4">
                <input class="form-control mb-2" v-model="newTodo" @keyup.enter="addTodo" placeholder="Add Todo">
                <button class="btn btn-primary" @click="addTodo" :disabled="newTodo.length === 0">Add</button>
            </div>
        </div>
        <hr>
        <div class="">
            <todo-item v-for="(todo, index) in todos" :key="todo.id" :todo="todo" :index="index"></todo-item>
            <div v-if="todos.length === 0">
                <p class="alert alert-info">There are no todos</p>
            </div>
        </div>
    </div>
</template>

<script>
    import todoItem from './TodoItem'

    export default {
        data() {
            return {
                todos: [],
                newTodo: '',
            }
        },
        created() {
            this.getTodos();
            this.initListeners();
        },
        methods: {
            initListeners() {
                const t = this;
                bus.$on('update-todo', function (details) {
                    t.updateTodo(details);
                });
                bus.$on('remove-todo', function (details) {
                    t.removeTodo(details);
                })
            },
            getTodos() {
                const t = this;
                axios.get('/todos')
                    .then(({data}) => {
                        t.todos = data;
                    });
            },
            createTodo(title) {
                const t = this;
                axios.post('/todos', {title: title, is_complete: false})
                    .then(({data}) => {
                        t.todos.unshift(data);
                    });
            },
            addTodo() {
                const t = this;
                if (t.newTodo.length > 0) {
                    t.createTodo(t.newTodo);
                    t.newTodo = '';
                }
            },
            updateTodo(details) {
                const t = this;
                axios.patch('/todos/' + details.id, details.data)
                    .then(({data}) => {
                        t.todos.splice(details.index, 1, data)
                    })
            },
            removeTodo(details) {
                const t = this;
                axios.delete('/todos/' + details.id)
                    .then(() => {
                        t.todos.splice(details.index, 1)
                    })
            },
        },
        components: {
            todoItem
        }
    }
</script>
