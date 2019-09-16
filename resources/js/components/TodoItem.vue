<template>
    <div class="row mb-4">
        <div class="col-12" v-show="state.edit === false">
            <div class="float-left col-1">
                <input type="checkbox" value="1" class="mr-2" v-model="data.is_complete" @click="updateTodo(!data.is_complete)">
            </div>
            <div class="float-left col-9">
                <label class="w-auto" :class="data.is_complete ? 'line-through text-success' : 'text-body'" @click="startEdit">{{todo.title}}</label>
            </div>
            <div class="float-left col-2">
                <button class="btn btn-sm btn-danger" @click="remove(index)">Delete</button>
            </div>
        </div>
        <div class="col-12" v-show="state.edit === true">
            <div class="float-left col-1">&nbsp;</div>
            <div class="float-left col-8">
                <input class="form-control col-12" v-model="data.title" @keyup.enter="updateTodo(data.is_complete)" placeholder="Update Todo">
            </div>
            <div class="float-left col-3">
                <button class="btn btn-primary btn-sm" @click="updateTodo(data.is_complete)" :disabled="data.title.length === 0">Update</button>
                <button class="btn btn-sm btn-danger" @click="cancelEdit">Cancel</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: ['todo', 'index'],
        data() {
            return {
                state: {
                    edit: false,
                },
                data: {
                    title: '',
                    is_complete: false,
                }
            }
        },
        mounted() {
            const t = this;
            t.data.title = t.todo.title;
            t.data.is_complete = t.todo.is_complete;
        },
        methods: {
            updateTodo(is_complete = false) {
                const t = this;
                t.$nextTick(() => {
                    t.data.is_complete = is_complete;
                    bus.$emit('update-todo', {data: t.data, index: t.index, id: t.todo.id});
                });
                t.state.edit = false;
            },
            remove() {
                const t = this;
                bus.$emit('remove-todo', {index: t.index, id: t.todo.id});
            },
            startEdit() {
                const t = this;
                if (t.data.is_complete === false) {
                    t.state.edit = true;
                }
            },
            cancelEdit() {
                const t = this;
                t.state.edit = false;
                t.data.title = t.todo.title;
            }
        }
    }
</script>
