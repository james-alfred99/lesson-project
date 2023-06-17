<template>
    <div v-if="this.lessons.length!==0" class="lesson-container">
        <div  v-for="item in this.lessons" :key="item._id" class="lessons">
           <LessonComp :data="item" @removeId="removeId($event)" :pageNo="this.pageNo" :cspaces="this.cspaces"/>
        </div>
    </div>
    <h1 v-else style="text-align: center; color: #ff1a1a;">Cart is Empty! Add Lessons to Cart</h1>

    <div class="checkout">
        <form @submit.prevent="checkOut">
            <div class="d-flex justify-content-center">
                Name:
                <label class="form-text-label ms-2">
                    <input class="form-text-input" type="text" name="name" v-model="myname" required />
                </label>
                <label class="form-text-label ms-2">
                    Phone
                    <input class="form-text-input" type="number" name="phone" v-model="phone" required />
                </label>
                <button class="btn btn-primary ms-2" @click="handleCheckout" id="checkout" :disabled="isDisabled">Checkout</button>
                
            </div>
        </form>
    </div>
    
</template>

<script>
import LessonComp from './LessonComp.vue'
import { toRaw } from 'vue'
    export default {
        name:"Checkout",
        props:['msg','lessons','pageNo','cspaces','cart'],
        components:{LessonComp},
        emits:['rId','emptycart'],
        data(){
            return{
                myname:"",
                phone:""
            }
        },
        methods:{
            removeId(id){
                this.$emit('rId',id)
            },
            handleCheckout(){
                if(this.myname=="" || this.phone=="")
                return
                if(/^[0-9]+$/.test(this.phone)==false || /^[a-zA-Z]+$/.test(this.myname)==false)
                 {
                    alert("input mismatch!!! Enter letters in name and numbers in phone")
                    this.myname=""
                this.phone=""
                    return
                 }
                alert(this.myname+" Your order with " +toRaw(this.cart.length)+" lessons placed successfully.")
                this.$emit('emptycart',{myname:this.myname,phone:this.phone})
                this.myname=""
                this.phone=""
            }
        },
        computed: {
            isDisabled() {
                if(this.myname.length==0 || this.phone.length==0)
                return true;
                else
                return false
                
            },
        },
        
    }
</script>

<style lang="scss" scoped>

</style>