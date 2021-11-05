<template>
    <div class="container">
        <div class="row">
          <div class="col-lg-4">
              <div class="form-group">
              <input type="text"
                  class="form-control" v-on:change="searchInput(searchText)" v-model="searchText" id="searchText" aria-describedby="helpId" placeholder="Search">
              </div>
          </div>
          <div class="col-lg-4">
            <div class="form-group">
              <select class="form-control" v-on:change="searchOption(searchSelect)" v-model="searchSelect"> 
                <option selected="selected" value="activeInactive">Active and Inactive</option>
                <option value="active">Active</option>
                <option value="inactive">Inactive</option>
              </select>
            </div>
          </div>
          <div class="col-lg-4">
            <div class="btn-group" id="group-btn" role="group" aria-label="">
                <router-link :to="{name:'Create'}" class="btn btn-info">CREATE NEW ROLE</router-link>
            </div>
          </div>
        </div>
        <br/><br/>
        <div class="row">
          <div class="col-lg-4"  v-for="role in rolesData" :key="role.id">
            <div class="card">
                <div class="card-body">
                  <h3>{{role.name}} 

                    <div v-if="role.active === false" class="btn-group" id="group-btn" role="group" aria-label="">
                        <button type="button" class="btn btn-danger">INACTIVE</button>
                    </div>

                  </h3>
                  <label>{{role.type}}</label>
                  <br><br>
                  <p>{{role.description}}</p>

                  <ul class="list-unstyled listImg">
                      <li class="d-inline" v-for="roleUser in role.users" :key="roleUser.id">
                        <img :src="roleUser.photo_url" />
                      </li>
                  </ul>

                </div>
                <div class="card-footer">
                    <label v-if="role.modified_on === ''" text-muted>Date created: {{role.created_on}}</label>
                    <label v-else>Last update: {{ role.modified_on }}</label>

                    
                    <div class="btn-group" id="group-btn" role="group" aria-label="">
                      <router-link v-if="role.editable === true" :to="{name:'Edit', params:{id:role.id}}" class="btn btn-info">Edit</router-link>
                      <button v-if="role.editable === true" type="button" v-on:click="deleteRole(role.id)" class="btn btn-danger">Delete</button>
                      <img v-else src="https://cdn4.iconfinder.com/data/icons/multimedia-75/512/multimedia-40-256.png">
                    </div>

                </div>
            </div>
            <br>
          </div>
        </div>
    </div>
</template>
<script>
  export default{
      data(){
          return{
              rolesData: [],
              originData:[],
              searchText: '',
              searchSelect: '',
          }
      },
      created:function(){
        this.getRoles();
      },
      methods:{
        getRoles(){
            fetch('http://localhost/roles/')
            .then(response=>response.json())
            .then((dataResponse) => {
                this.rolesData = [];
                if(typeof dataResponse[0].success === 'undefined'){ 
                    this.rolesData = dataResponse;
                    this.originData = dataResponse;
                }
            })
            .catch(console.log)
        },
        deleteRole(id){
          
          let indexID = 0;
          this.rolesData.forEach((el, index) => {
            if (el.id === id) indexID =  index;
          });

            this.rolesData.splice(indexID, 1);
            fetch('http://localhost/roles/?delete',{
                method:"POST",
                body:JSON.stringify(this.rolesData)
            })
            .then(response => response.json())
            .then((dataResponse => {
                console.log(dataResponse);
            }))
            .catch(console.log)
        },
        searchOption(select){
          var currData = this.originData;
          this.rolesData = [];

          this.searchText = '';
          
          if(select === 'active'){
            currData.forEach(element => {
              if(element.active === true){
                this.rolesData.push(element);
              }
            });
          }
          else if(select === 'inactive'){
            currData.forEach(element => {
              if(element.active === false){
                this.rolesData.push(element);
              }
            });
          }else{
            currData.forEach(element => {
              this.rolesData.push(element);
            });
          }
        },
        searchInput(text){
          if(text){
            return this.rolesData.filter((role)=>{
              return text.toLowerCase().split(' ').every(v => role.name.toLowerCase().includes(v));
          })
          }else{
            return this.rolesData;
          }
        }
      }
  }
</script>
<style scoped>
 .card{
   text-align: left;
 }
 .listImg li:not(:first-child){
   padding-left: 10px; ;
 }
 .listImg li img{
   border-radius: 50%;
 }
 #group-btn{
    float: right;
    }
#group-btn img{
  width:50px;
  height: 50px;
}
</style>