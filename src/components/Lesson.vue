
<template>
    <div style="text-align: center;">
       <h2 style="color: black;">Welcome to Lessons!</h2>
       <div id="search">
         <input type="text" v-model="getinput" placeholder="search items...">
       </div>
    </div>
    <div  class="lesson-container">
        <div v-for="item in this.lessons" :key="item._id"  class="lessons" >
           <LessonComp :data="item" @addId="addId($event)" :pageNo="this.pageNo" :lspaces="this.lspaces" v-if="item.name.includes(getinput) || item.price.includes(getinput)  || item.location.includes(getinput)" /> 
        </div>
    </div>
</template>

<script>
    
import LessonComp from './LessonComp.vue'
    
    export default {
     name:"Lesson",
     props:['msg','lessons','pageNo','lspaces','sortby','orderby'],
     
     emits:['aId'],
     components:{LessonComp},
     data(){
        return{
          space:0,
          getinput:"",
          srch:"",
          ind:"2"
        }
     },
     methods:{
        addId:function(id){
           this.$emit('aId',id)
        },
        handleSearch(){
         this.srch=this.getinput 
        }
     },
  
}
      
</script>

<style lang="scss" scoped>

</style>