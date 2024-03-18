<script setup>
import axios from 'axios';
</script>

<template>
  <main>
    <div class="container mt-5">
      <div class="card">
        <div class="card-header d-flex align-items-center justify-content-between">
          <h3>
            Students List
          </h3>
          <button  type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
            Add New Student
          </button>
        </div>
        <div class="card-body">
          <table class="table table-striped-columns table-primary ">
            <thead>
              <tr>
                <td>ID</td>
                <td>Name</td>
                <td>Course</td>
                <td>Email</td>
                <td>Phone</td>
                <td>Adress</td>
                <td>Update</td>
                <td>Delete</td>
              </tr>
            </thead>
            <tbody v-if="this.students.length > 0" class="rowsContainer">
              <tr v-for="(student,index) in this.students" :key="index">
                <td>{{ student.id }}</td>
                <td>{{ student.name }}</td>
                <td>{{ student.course }}</td>
                <td>{{ student.email }}</td>
                <td>{{ student.phone }}</td>
                <td>{{ student.address }}</td>
                <td>
                  <button data-bs-toggle="modal" data-bs-target="#editModal" v-on:click="editRow(index)"  class="btn btn-success w-100">
                    Edit
                  </button>
                </td>
                <td>
                  <button v-on:click="deleteRow(student.id)" class="btn btn-danger w-100">
                    Delete
                  </button>
                </td>
              </tr>
            </tbody>
            <tbody v-else="this.students.length < 0 ">
              <tr>
                <td colspan="8">
                  loading...
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- add new row modal -->
<!-- Modal -->
  <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Add New Student</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <form action="" v-on:submit.prevent>
          <div class="modal-body">
          <input type="text" id="name" v-model="newData.name" required placeholder="Enter Name">
          <input type="text" id="course" v-model="newData.course"  placeholder="Enter course">
          <input type="email" id="email" v-model="newData.email" required placeholder="Enter email">
          <input type="text" id="phone" v-model="newData.phone"  placeholder="Enter Phone">
          <input type="text" id="address" v-model="newData.address"  placeholder="Enter Address">

          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button type="submit" class="btn btn-primary" v-on:click="addStudent()">Save changes</button>
          </div>
        </form>
      </div>
    </div>
  </div>

<!-- edit row modal -->
  <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel"> Edit Student</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <form action="" v-on:submit.prevent>
          <div class="modal-body">
          <input type="text" id="name" v-model="editedRow.name" required placeholder="Enter Name">
          <input type="text" id="course" v-model="editedRow.course"  placeholder="Enter course">
          <input type="email" id="email" v-model="editedRow.email" required placeholder="Enter email">
          <input type="text" id="phone" v-model="editedRow.phone"  placeholder="Enter Phone">
          <input type="text" id="address" v-model="editedRow.address"  placeholder="Enter Address">

          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button type="submit" class="btn btn-primary" v-on:click="saveEdits(editedRow.id)">Save Edits</button>
          </div>
        </form>
      </div>
    </div>
  </div>


  <!-- toaster -->
  <div class="toast-container position-fixed bottom-0 end-0 p-3">
  <div id="addedsuccessfully" class="toast bg-success text-white text-center" role="alert" aria-live="assertive" aria-atomic="true">
    <div class="toast-body">
      {{message.title}}
    </div>
  </div>
</div>
  </main>
</template>



<script>
  
  import axios from 'axios';
  export default {
    data (){
      return {
        url : "https://crud-test-lwrq.onrender.com/students",
        students : [],
        newData : {
          id: "" ,
          name:'',
          course:"",
          email:"",
          phone:"",
          address:""
        },
        editedRow:{
          id:"",
          name:'',
          course:"",
          email:"",
          phone:"",
          address:""
        },
        message : {
          color : "",
          title:""
        }
        
      }
    },
    mounted (){
      this.getData()
    },
    methods : {
      async getData(){
        let res = await fetch(this.url)
        this.students = await res.json()
      },
      async addStudent(){
        if(this.newData.name !='' && this.newData.email !='') {
          if(this.students.length > 0) {
            this.newData.id = (++this.students[this.students.length-1].id).toString()
          }
          else {
            this.newData.id = "1"
          }
        let res = await fetch(this.url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(this.newData)
        }
        )
        this.getData()
        let result =  res.status;
        const addedsuccessfully = document.getElementById('addedsuccessfully')
        if( result == 201 ) {
          this.message.title = "Added Successfully"
          addedsuccessfully.classList.add("show")
          setTimeout(() => {
          addedsuccessfully.classList.remove("show")
          this.newData.name=''
          this.newData.course=''
          this.newData.phone=''
          this.newData.email=''
          this.newData.address=''
          }, 2000);

        }
        }
        else {
          console.log("error data");
        }
      },

      editRow(index){
        this.editedRow.id = this.students[index].id
        this.editedRow.name = this.students[index].name
        this.editedRow.course = this.students[index].course
        this.editedRow.email = this.students[index].email
        this.editedRow.phone = this.students[index].phone
        this.editedRow.address = this.students[index].address
      },

      async saveEdits(idEdit){
        let res = await fetch(`${this.url}/${idEdit}`, {
                method: 'PUT',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(this.editedRow)
        }
        )  
        this.getData()
        const addedsuccessfully = document.getElementById('addedsuccessfully')
          this.message.title = "Updated Successfully"
          addedsuccessfully.classList.add("show")
          setTimeout(() => {
          addedsuccessfully.classList.remove("show")
          this.newData.name=''
          this.newData.course=''
          this.newData.phone=''
          this.newData.email=''
          this.newData.address=''
          }, 2000);
      },

      async deleteRow(num) {
        let res = await fetch(`${this.url}/${num}`, {
                method: 'DELETE',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(this.editedRow)
        }
        )  
        this.getData()
        const addedsuccessfully = document.getElementById('addedsuccessfully')
          this.message.title = "Deleted Successfully"
          addedsuccessfully.classList.add("show")
          setTimeout(() => {
          addedsuccessfully.classList.remove("show")
          this.newData.name=''
          this.newData.course=''
          this.newData.phone=''
          this.newData.email=''
          this.newData.address=''
          }, 2000);
    }
  }
  }
  
</script>

<style scoped>
  .table {
    text-align: center;
  }

  thead td {
    font-weight: bolder;
  }

  input {
    display: block;
    width: 100%;
    border: 1px solid  #ccc;
    padding: 5px 10px;
    margin-block: 20px;
    border-radius: 5px;
    outline: none;
  }
  input:focus {
    border: 1px solid rgb(65, 141, 185);
    box-shadow: 5px 5px 5px white;
  }

  .toast-container {
    transition: all .5 cubic-bezier(0.175, 0.885, 0.32, 1.275);
  }
</style>
