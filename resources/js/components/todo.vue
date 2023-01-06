<template>
    <div class="container-fluid mx-0 px-0" >
        <nav class="navbar bg-body-tertiary bg-primary">
        <div class="container-fluid">
            <a class="navbar-brand" href="#">Manage Users</a>
        </div>
        </nav>
        <div class="row justify-content-center m-2">
            <div class="col-md-8">
                <div class="card"> 
                    <div class="card-header"><strong>Users</strong></div>
                
                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" placeholder=" " class="form-control rounded" aria-label="todo" 
                            aria-describedby="todo" v-model="todo_input">
                            <div class="input-group-append m-1">
                                <button v-if="!edit_todo_id" type="button" class="btn btn-info" @click="saveTodo()">Add Users</button>
                                <button v-else type="button" class="btn btn-info" @click="updateTodo()">Update</button>
                            </div>
                        </div>
                            <button style="float:right" type="button" class="btn btn-sm text-danger " @click="resetTodo()">
                            Reset</button>
                        <table class="table table-bordered mt-4">
                            <thead>
                                <th>S.no</th>
                                <th>Name</th>
                                <th>Action</th>
                            </thead>
                            <tbody>
                                <tr v-for="(todo, index) in todos" :key='index'>
                                    <td>{{++index }}</td>
                                    <td>{{ todo.name }}</td>
                                    <td>
                                        <button class="btn btn-danger" type="button" @click="deleteTodo(--index)">
                                        Delete</button>
                                        <button class="btn btn-primary" type="button" @click="editTodo(--index)">
                                        Edit</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
</template>

<script>
    export default {
        data(){
            return{
                todos:[],
                api:'http://localhost:8000/api/todos',
                todo_input:'',
                edit_todo_id:'',
                edit_index:''
            }
        },
        mounted(){
            //get api data

            this.axios.get(this.api).then(res =>{
                this.todos=res.data
            });
        },
        methods:{
            saveTodo(){
               if(this.todo_input.length > 0){
                let data= {'name': this.todo_input}
                this.axios.post(this.api, data).then(res =>{
                    this.todos.push(res.data);
                    this.todo_input='';
                    });            
               }
            },
            deleteTodo(index){
                if(this.todos[index].id){
                    this.axios.delete(this.api+'/'+this.todos[index].id).then(res=>{
                        this.todos.splice(index,1);
                    });
                }
            },
            editTodo(index){
                if(this.todos[index].id){
                    this.todo_input=this.todos[index].name;
                    this.edit_todo_id=this.todos[index].id;
                    this.edit_index=index;
                }
            },
            updateTodo(){
                if(this.todo_input.length > 0){
                let data= {'name': this.todo_input}
                this.axios.patch(this.api+'/'+ this.todos[this.edit_index].id,data).then(res =>{
                    // this.todos.push(res.data);
                        this.todos[this.edit_index].name=res.data.name;
                    // this.todo_input='';
                        this.resetTodo();
                    });            
               }
            },
            resetTodo(){
                this.edit_todo_id='',
                this.edit_index='',
                this.todo_input=''
            }
        }
    }
</script>
