<template>
    <div>
        <h1>My Tasks</h1>
        <h4>New Task</h4>
        <form action="#" @submit.prevent="edit ? updateTask(task.id) : createTask()">
            <div class="input-group">
                <input v-model="task.body" ref="taskinput" type="text" name="body" class="form-control" autofocus>
                <span class="input-group-btn">
                    <button v-show="!edit" type="submit" class="btn btn-primary">New Task</button>
                    <button v-show="edit" type="submit" class="btn btn-primary">Edit Task</button>
                </span>
            </div>
        </form>
        <br/>
        <h4>All Tasks</h4>
        <ul class="list-group">
            <li class="list-group-item" v-for="task in list">
                {{ task.body }}
                <button @click="showTask(task.id)" class="btn btn-primary btn-xs">Edit</button>
                <button @click="deleteTask(task.id)" class="btn btn-danger btn-xs">Delete</button>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        data: function() {
            return {
                edit: false,
                list: [],
                task: {
                    id: '',
                    body: ''
                }
            }
        },

        mounted: function() {
            this.$nextTick(function () {
                this.fetchTaskList()
            })
        },

        methods: {
            fetchTaskList: function() {
                axios.get('api/tasks').then(response => {
                    this.list = response.data
                })
            },

            createTask: function() {
                if (this.task.body) {
                    axios.post('api/task/store', this.task).then(() => {
                        this.fetchTaskList()
                    })
                }
                this.edit = false
            },

            updateTask: function(id) {
                axios.patch('api/task/' + id, this.task).then(() => {
                    this.fetchTaskList()
                })
                this.edit = false
            },

            showTask: function(id) {
                axios.get('api/task/' + id).then(response => {
                    this.task.id = response.data.id
                    this.task.body = response.data.body
                })
                this.$refs.taskinput.focus()
                this.edit = true
            },

            deleteTask: function(id) {
                axios.delete('api/task/' + id).then(() => {
                    this.fetchTaskList()
                })
            },
        }
    }
</script>
