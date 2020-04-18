<template>
  <div class="container-fluid">
  <nav class="navbar navbar-light bg-light">
  <a class="navbar-brand" href="#">rafiki</a>
   <span v-shortkey="['ctrl', 'l']" @shortkey="showModal()"></span>
  <div class="navbar-text" >
    <button class="btn btn-outline-light btn-sm rounded-pill" @click="logout">Logout</button>
  </div>
</nav>
<div class="jumbotron">
  <p class="display-4">Hello, <u>{{firstname}}</u>!</p>
  <div class="alert alert-secondary alert-dismissible fade show" v-show="alerting == true" role="alert" v-if="!isMobile()">
  <strong>For keyboard shortcuts list press:</strong> ctrl + L <span>anywhere</span>.
  <button type="button" class="close" data-dismiss="alert" aria-label="Close">
    <span aria-hidden="true">&times;</span>
  </button>
    </div>
    <div class="card text-center paddi" @click="createNew">
      <div class="card-body">
        <i class="fas fa-plus"></i>
        <p>New Doc</p>
      </div>
    </div>
</div>
<div align="center">
  <div class="row row-cols-1 row-cols-md-4">
  <div class="col mb-2 card-deck" v-for="(docu, index) in docus" :key="index">
    <div class="card">
      <div class="card-body" @click="loadDoc(docu._id, docu.shareId)">
        <h5 class="card-text">{{docu.docName}}</h5>
        <p class="card-text">Last edited on: </p>
      </div>
      <button type="button" class="close" @click="deleteDoc(docu._id)"><span aria-hidden="true">&times;</span></button> 
    </div>
  </div>
</div>
</div>
<div class="jumbotron"></div>
 <div v-shortkey="['esc']" @shortkey="closeModal()" class="modal" style="display: block" v-if="showme">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h4 class="modal-title">
          Rafiki command center.
        </h4>
      </div>
      
      <div class="modal-body">
        Load command center (Ctrl + L) <br>
        Search (Ctrl + F) <br>
        Save file (Ctrl + s, esc)
      </div>
      
      <div class="modal-footer">
        tap 'esc' to exit
      </div>
    </div>
  </div>
</div>

  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Dashboard',
  data() {
    return{
      firstname: sessionStorage.getItem('firstname'),
      id: sessionStorage.getItem('id'),
      docus: [],
      showme: false,
      alerting: sessionStorage.getItem('alert')
    }
  },
  methods: {
    createNew() {
      var creator = this.id
      var docname = 'Name your file'
      var docontent = 'tap to start editing (shortcut: tap "esc" to save)'
      axios.post('http://localhost:3000/api/createdoc', {
        docName: docname,
        creator: creator,
        docContent: docontent
      }).then( res => {
        console.log(res.data)
        sessionStorage.setItem('update_id', res.data._id)
        sessionStorage.setItem('docTitle', res.data.docName)
        sessionStorage.setItem('docIntro', res.data.docTitle)
        sessionStorage.setItem('docContent', res.data.docContent)
        this.$router.push('/doc')
        var update_id = res.data._id
        var share_id = '/' + res.data._id;
        console.log(share_id)
        axios.post('http://localhost:3000/api/updatedocument', {
                 id: update_id,
                 shareId: share_id
             }).then( resp => {
                 console.log('share_id created')
                 console.log(resp.data.shareId)
                 sessionStorage.setItem('shareId', resp.data.shareId)
             }).catch(err => {
                 console.log(err)
          })
      }).catch(err => {
        console.log(err);
      })
    },
    showModal (){
      this.showme = true
    },
    closeModal (){
      this.showme = false
    },
    isMobile() {
   if(/Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent)) {
     return true
   } else {
     return false
   }
 },
loadDoc(id, shareId){
      sessionStorage.setItem('update_id', id)
      sessionStorage.setItem('shareId', shareId)
      this.$router.push('/doc')
},
    deleteDoc(id){
      axios.post('http://localhost:3000/api/deletedoc', {
        id: id
      }).then( res => {
        console.log(res.data)
        console.log('Deleted')
      }).catch( err => {
        console.log(err)
      })
    },
    logout() {
      sessionStorage.clear()
      this.$router.push('/')
    },
    findAllDoc() {
      var creator = this.id
      axios.post('http://localhost:3000/api/findall', {
        creator: creator
      }).then( res => {
        console.log(res.data)
        this.docus = res.data
      }).catch( err => {
        console.log(err)
      })
    }
  },
  mounted(){
    this.findAllDoc()
  }
}
</script>

<style lang="css" scoped>
 .jumbotron {
   height: 18rem;
   background: transparent!important;
   color: rgb(250, 243, 243);
 }
 .card {
  background: rgb(241, 242, 245);  
 }
 .modal {
   background: black;
   color: rgb(7, 7, 7);
   opacity: 0.7;
 }
 .modal-content {
     background: black;
     color: white;
 }
 .container-fluid {
     background-image: linear-gradient( #19191a 60%, rgb(255, 255, 255) 40%);
     height: 100%;
 }
 .paddi {
   max-width: 25% !important;
   background:rgb(241, 242, 245);
   color: rgb(43, 40, 40);
 }
 .btn:focus,.btn:active {
   outline: none !important;
   box-shadow: none !important;
}
.navbar {
    background: transparent!important;
}
.navbar-brand{
  color: white !important;
}
</style>