<template>
<div class="container-fluid">
<nav class="navbar navbar-light bg-light">
    
    <a class="navbar-brand" href="#">
        <span contenteditable class="header" v-text="docTitle"
          @blur="editTitle" @keydown.enter="endEdit">
        </span>
        <span v-show="!firstname" style="color: white;">rafiki</span>
    </a>

    <span v-shortkey="['ctrl', 'l']" @shortkey="showModal()"></span>
    <div class="navbar-text">
<button class="btn btn-outline-light btn-sm rounded-pill" @click="share">Share</button>       
<button class="btn btn-outline-light btn-sm rounded-pill" @click="logout" v-show="firstname">Logout</button>
    </div>
        
</nav>
 

<div class="row">
    <div class="col-sm-2">
    <button class="btn btn-outline-light btn-sm rounded-pill" @click="back" v-show="firstname"><i class="fas fa-arrow-left"></i></button>
    <button class="btn btn-outline-light btn-sm rounded-pill" @click="saveContent">Save</button>
    </div> <br><br>
       <div class="card mb-3 col-sm-9 border-secondary">
            <div  class="btn-group">
                <button type="button" class="btn btn-default" @click="exec('bold')">
                bold
                </button>

                <button type="button" class="btn btn-default" @click="exec('italic')">
                italic
                </button>  
            <button class="btn btn-default dropdown-toggle" data-toggle="dropdown" title="Font Size">
                font size &nbsp;<b class="caret"></b>
            </button>
            <ul class="dropdown-menu">
                <li @click="exec('fontSize', 5)"><button  class="btn btn-sm btn-outline" @click="exec('fontSize', 5)" >Large</button></li>
                <li @click="exec('fontSize', 3)"><button  class="btn btn-sm btn-outline" @click="exec('fontSize', 3)">Normal</button></li>
                <li @click="exec('fontSize', 1)"><button  class="btn btn-sm btn-outline" @click="exec('fontSize', 1)">Small</button></li>
            </ul>

                <button type="button" class="btn btn-default" @click="exec('underline')">
                    underline
                </button> 
        </div>
           <div class="card-body">
            <div contenteditable ="true" class="content card-text" v-html="docContent"
      @blur="editContent" @keydown.esc.exact="saveContent" @keydown.alt="exec('selectAll')" autofocus></div>
           </div>
       </div>
  
</div>



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
        Save file (Ctrl + S, esc)
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
  name: 'Home',
  data() {
    return{
      docContent:sessionStorage.getItem('docContent'),
      docTitle: sessionStorage.getItem('docTitle'),
      update_id: sessionStorage.getItem('update_id'),
      docIntro: sessionStorage.getItem('docIntro'),
      firstname: sessionStorage.getItem('firstname'),
      showme: false,
      share_id: sessionStorage.getItem('shareId')
    }
  },
  methods: {
    back(){
          this.$router.push('/dashboard')
        },
        share(){
            var socket_namespace = this.share_id;
            console.log(socket_namespace)
        },
        showModal (){
      this.showme = true
    },
    closeModal (){
      this.showme = false
    },
        editTitle(evt){
             var src = evt.target.innerText
             this.docTitle = src
         },
          exec (command, arg) {
            document.execCommand(command , false , arg)
        },
         endEdit(){
             this.$el.querySelector('.header').blur()
              var id = this.update_id
              var docName = this.docTitle
               axios.post('http://localhost:3000/api/updatedocument', {
                 id: id,
                 docName: docName
             }).then(res => {
                 console.log('updated')
             }).catch( err => {
                 console.log(err)
             })
         },
         editContent(evt){
             var src = evt.target.innerHTML
             this.docContent = src
         },
         saveContent(){
             this.$el.querySelector('.content').blur()
             this.$el.querySelector('.header').blur()
             var id = this.update_id
             var docContent = this.docContent
             axios.post('http://localhost:3000/api/updatedocument', {
                 id: id,
                 docContent: docContent
             }).then(res => {
                 console.log('saved')
             }).catch( err => {
                 console.log(err)
             })
            
         },
         getOne() {
             var update_id = this.update_id
             axios.post('http://localhost:3000/api/findone', {
                 id: update_id
             }).then( res => {
                 console.log('found')
                 this.docContent = res.data.docContent
                 this.docTitle = res.data.docName
                 this.docIntro = res.data.docTitle
             }).catch(err => {
                 console.log(err)
             })
         },
         logout() {
            sessionStorage.clear()
            this.$router.push('/')
        }
  },
  mounted(){
      this.getOne()
  }
}
</script>
<style lang="css" scoped>
.btn:focus,.btn:active {
   outline: none !important;
   box-shadow: none !important;
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
  background-image: linear-gradient( #19191a 60%, rgb(219, 218, 218) 40%);
  height: 100%;
}
.navbar {
    background: transparent!important;
}
.header {
    background: transparent;
    color: white;
}
.content{
    height: 30rem !important;
    background: transparent;
    outline: transparent ;
    overflow-y: auto;
    white-space: pre-wrap;
}
.card {
    background-color: rgb(241, 242, 245);
    border: transparent;
}
</style>
<!--background-image: linear-gradient(45deg, #6666d1 50%, rgb(232, 238, 255) 50%);-->