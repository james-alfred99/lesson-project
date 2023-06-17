<template>
    <!-- <p>{{ this.lspaces }}</p> -->
    <div class="lesson-comp">
        <img v-bind:src="`${this.url}`" style="height: 50px;width: 50px;">
       <p >Name: {{ this.data.name }}</p>
       <p>Price: {{ this.data.price }}</p>
       <p>Location: {{ this.data.location }}</p> 
       <div v-if="this.pageNo===1" v-for="items in this.lspaces" key="items._id">
          <p v-if="items._id===this.data._id">Spaces: {{ items.spaces}}</p>
          <button @click="handleAdd" v-if="this.pageNo===1 && items._id===this.data._id" :disabled="items.spaces<1" class="btn btn-primary ms-2" >Add to Cart</button>
       </div>
       <div v-if="this.pageNo===2" v-for="items in this.cspaces" key="items._id">
          <p v-if="items._id===this.data._id">spaces: {{ items.spaces}}</p>

       </div>
       <button @click="handleRemove" v-if="this.pageNo===2" id="remove-btn">Remove</button>
    </div>
</template>

<script>
import { toRaw } from 'vue'

    export default {
        name:'LessonComp',
        props:['data','addId','removeId','pageNo','lspaces','cspaces'],
        data(){
            return{
                url:""
            }
        },
        methods:{
            handleAdd:function(){
                
                this.$emit('addId',this.data._id)
                this.$forceUpdate()
            },
            handleRemove(){
                this.$emit('removeId',this.data._id)
                this.$forceUpdate()
            }
        },
        computed:{
            makeDisable(){
                toRaw(this.lspaces).forEach(item=>{
                    if(item._id==this.data._id && item.spaces==0)
                    {
                        console.log("disabled")
                        return true
                    }
                })
                return false
            }
        },
        mounted(){
            this.url=this.data.image
           
        }
       
    }
</script>

<style lang="scss" scoped>
 
</style>