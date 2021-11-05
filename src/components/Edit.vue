<template>
    <div class="container">
        <div class="card">
            <div class="card-header">
                EDIT ROLE
            </div>
            <div class="card-body">
                <form v-on:submit.prevent="updateRole">
                    <div class="row">
                        <div class="col-lg-6">
                            <div class="form-group">
                            <label for="name">Name:</label>
                            <input type="text"
                                class="form-control" required name="name" v-model="role.name" id="name" aria-describedby="helpId" placeholder="name">
                            </div>
                        </div>
                        <div class="col-lg-6">
                            <div class="form-group">
                            <label for="type">Type</label>
                            <input type="text"
                                class="form-control" required name="type" v-model="role.type" id="type" aria-describedby="helpId" placeholder="Type">
                            </div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-lg-12">
                            <div class="form-group">
                            <label for="description">Description</label> 
                            <textarea class="form-control" name="description" v-model="role.description" id="description" rows="3"></textarea>
                            </div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-lg-2">
                            <div class="form-group">
                                <div class="form-check">
                                <input class="form-check-input" value="1" name="editable" v-model="role.editable" type="checkbox" id="editable">
                                <label class="form-check-label" for="editable">
                                    Editable
                                </label>
                                </div>
                            </div>
                        </div>
                        <div class="col-lg-2">
                            <div class="form-group">
                                <div class="form-check">
                                <input class="form-check-input" value="1"  name="active" v-model="role.active" type="checkbox" id="active">
                                <label class="form-check-label" for="active">
                                    Active
                                </label>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="btn-group" role="group" aria-label="">
                        <button type="submit" class="btn btn-success">SUBMIT</button>
                        <router-link :to="{name:'Home'}" class="btn btn-danger">Cancelar</router-link>
                    </div>
                </form>
            </div> 
        </div>
    </div>
</template>
<script>

import json from '../json/user_roles.json';
export default ({
    data(){
        return{
            role: {},
            rolesData: json,
            timestamp: '',
            currId: this.$route.params.id
        }
    },
    created:function(){
        this.getNow();
        this.getRole();
    },
    methods:{
        getRole(){
            
            fetch('http://localhost/roles/')
            .then(respuesta=>respuesta.json())
            .then((datosRespuesta) => {
              datosRespuesta.forEach(element => {
                  if(element.id == this.currId){
                      this.role = element;
                  }
              });
            })
            .catch(console.log)
            
        },
        updateRole(){

            this.rolesData.forEach(element => {
                if(element.id == this.currId){
                    var dataToEdit = {id: this.currId, name: this.role.name, type:this.role.type, description:this.role.description, 
                    editable:this.role.editable, active:this.role.active, users:this.users, created_on: element.created_on, modified_on: this.timestamp};
                    

                    
                    let indexID = 0;
                    this.rolesData.forEach((el, index) => {
                        if (el.id === this.currId) indexID =  index;
                    });
                    
                    this.rolesData.splice(indexID, 1, dataToEdit);
                }
            });

            
            fetch('http://localhost/roles/?update',{
                method:"POST",
                body:JSON.stringify(this.rolesData)
            })
            .then(response => response.json())
            .then((dataResponse => {
                console.log(dataResponse);
            }))
            .catch(console.log);
            
            window.location.href="../";
            
        },
        getNow() {
            const today = new Date();
            const date = today.getFullYear()+'-'+(today.getMonth()+1)+'-'+today.getDate();
            //const time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
            const dateTime = date;
            this.timestamp = dateTime;
        }
    }
})
</script>
<style>
.form-group{
    text-align:left;
}
</style>