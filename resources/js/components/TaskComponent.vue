
<template>

<div>

        <button @click="loadCreateModal" class="btn btn-primary btn-block">Add New Task</button>

        <table class="table" v-if="tasks">
            <thead>
                <tr>
                    <th>Id</th>
                    <th>Name</th>
                    <th>Body</th>
                </tr>
            </thead>
            <tbody v-for="(task, index) in tasks">
                <tr>
                    <th>{{index + 1}}</th>
                    <th>{{task.name}}</th>
                    <th>{{task.body}}</th>
                    <th><button @click="loadUpdateModal(index)" class="btn btn-info">Edit</button></th>
                    <th><button @click="deleteTask(index)" class="btn btn-danger">Delete</button></th>
                </tr>
            
            </tbody>
        </table>


        <!-- Button trigger modal -->
<!-- <button type="button" class="btn btn-primary" data-toggle="modal" data-target="#exampleModal">
  Launch demo modal
</button> -->

<!--Create Modal -->
<div class="modal fade" id="create-modal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Create Modal</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">

        <div class="alert alert-danger" v-if="errors.length > 0">
            
            <small><h4><b>Careful!</b></h4><p v-for="error in errors">{{error}}</p></small>
        </div>
  
        <div class="input group">
            <label for="name">Name</label>
            <input v-model="task.name" type="text" id="name" class="form-control">
        </div>

         <div class="input group">
            <label for="dscription">Body</label>
            <input v-model="task.body" type="text" id="description" class="form-control">
        </div>
        
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
        <button @click="createTask()" type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>



<!--Update Modal -->
<div class="modal fade" id="update-modal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Update Modal</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">

        <div class="alert alert-danger" v-if="errors.length > 0">
            
            <small><h4><b>Careful!</b></h4><p v-for="error in errors">{{error}}</p></small>
        </div>

        
       
        <div class="input group">
            <label for="name">Name</label>
            <input v-model="new_update_task.name" type="text" id="name" class="form-control">
        </div>

         <div class="input group">
            <label for="dscription">Body</label>
            <input v-model="new_update_task.body" type="text" id="description" class="form-control">
        </div>
        
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
        <button @click="updateTask" type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>


    </div>
              
</template>

<script>
    export default {
        data() {
            return {
                task:{
                    name: '',
                    body: ''
                },
                tasks: [],
                uri: 'http://localhost:8000/tasks/',
                errors: [],
                new_update_task: []
            }
    },

    methods:{
        loadCreateModal(){
            $("#create-modal").modal("show");
        },

        loadUpdateModal(index){
            this.errors = [];
            $("#update-modal").modal("show");
            this.new_update_task = this.tasks[index];
        },

        createTask(){
             
            axios.post(this.uri, {name: this.task.name , body:this.task.body}).then(response=>{
               
                this.tasks.push(response.data.task);
                
                $("#create-modal").modal("hide");
                
            })
            .catch(error=>{
                this.errors = [];
                if(error.response.data.errors.name){
                    this.errors.push(error.response.data.errors.name[0]);
                }
                if(error.response.data.errors.body){
                    this.errors.push(error.response.data.errors.body[0]);
                }
        });
        },

        updateTask(){
            axios.patch(this.uri + this.new_update_task.id, {
                name: this.new_update_task.name,
                body: this.new_update_task.body,

            }).then(response =>{
                $("#update-modal").modal("hide");
        }).catch(error=>{
            this.errors = [];
                if(error.response.data.errors.name){
                    this.errors.push(error.response.data.errors.name[0]);
                }
                if(error.response.data.errors.body){
                    this.errors.push(error.response.data.errors.body[0]);
                }
            
        });
        
        },

        deleteTask(index){
            let confirmBox = confirm("Are you sure you want to delete this?");

            if(confirmBox == true){
                axios.delete(this.uri + this.tasks[index].id)
                    .then(response=>{
                        this.$delete(this.tasks, index);
                    });
                
            }
            
        },

        loadTasks(){
            axios.get(this.uri).then(response=>{
                this.tasks=response.data.tasks
            });
        }
    },
    mounted(){
        this.loadTasks();
    },

    resetData(){
        this.task.name = '';
        this.task.body = '';
        
    }

    
}
</script>
