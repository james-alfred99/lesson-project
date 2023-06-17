
<template>
  <div class="header">
    <div id="sorting" v-if="page==1">
      <div class="row">
                <h5>Sort by</h5>
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="sort-by" v-model="sortby" value="name" />
                    Name
                </label>
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="sort-by" v-model="sortby" value="location" />
                    Location
                </label>
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="sort-by" v-model="sortby" value="price" />
                    Price
                </label>
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="sort-by" v-model="sortby" value="spaces" />
                    Availability
                </label>
            </div>
            <div class="row mt-5">
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="order" v-model="orderby" value="asc" />
                    Ascending
                </label>
                <label class="form-check-label">
                    <input class="form-check-input" type="radio" name="order" v-model="orderby" value="desc" />
                    Descending
                </label>
            </div>
    </div>  
    <h2 v-if="page==0">Home </h2>
    <h2 v-if="page==1">Lessons </h2>
    <h2 v-if="page==2">Checkout</h2>
    <div class="right">
      <button @click="page=0" id="btn0">Home</button>
      <button @click="page=1" id="btn1">Lesson</button>
      <button @click="page=2" id="btn2" :disabled="doDisable">Checkout</button>
      <button class="btn btn-primary ms-2" style="width: 100px;" @click="myCart=!myCart" :disabled="doDisable">cart</button>
      <h1 v-if="this.myCart===true">{{ cart.length }}</h1>
    </div>
    
  </div>
  <h1 v-if="page===0" style="text-align: center;">
    <h1>My Cart</h1>
    <button class="cart-btn-2" @click="myCart=!myCart" :disabled="doDisable"><img src="../src/assets/download.jpg" alt=""></button>
    <h1 v-if="this.myCart===true" style="color: #ff1a1a;"><small style="font-size: 0.5em;color: black;">Items in cart: </small>{{ cart.length }}</h1>
  </h1>
  <Lesson v-if="page===1" msg="List of all Lessons!!" :lessons="sortedLessons" @aId="addToCart($event)" :pageNo="this.page" :lspaces="lspaces" :sortby="sortby" :orderby="orderby"/>
  <Checkout v-if="page===2" msg="" :lessons="cart" @rId="removeFromCart($event)" :pageNo="this.page" :cspaces="cspaces" :cart="cart" @emptycart="emptyCart($event)"/>
  <!-- <Checkout v-if="page===3" :cart="cart" @emptycart="emptyCart()"/> -->
</template>

<script>
  
  import Lesson from './components/Lesson.vue'
  
  import Checkout from './components/Checkout.vue'
  import axios from 'axios'
  import { isProxy, toRaw } from 'vue';
  
  
  export default{
  name:"App",
  components:{Lesson,Checkout},
  data(){
    return{
      page:0,
      lessons:[],
      filter:[],
      cart:[],
      lspaces:[],
      cspaces:[],
      sortby:"name",
      orderby:"asc",
      myCart:false,
      orders:{myorders:[]},
      updates:[]
    }
  },
  methods:{
     addToCart(id){
      
      toRaw(this.lspaces).forEach(item=>{
         if(item._id===id)
           {
            if(item.spaces===0)
            return
            item.spaces--;
           
            //console.log(item.spaces)
          }
         })
         
         toRaw(this.cspaces).forEach(item=>{
         if(item._id===id && item.spaces<item.max )
           {
            item.spaces++;
            //console.log(item.spaces)
          }
         })
        //console.log(this.lspaces) 
     if(this.cart.find(item=>item._id===id))
        return
     this.cart.push(toRaw(this.lessons).find(item=>item._id===id))
     
     
   },
   removeFromCart(id){
    
    toRaw(this.cspaces).forEach(item=>{
      //.log("remove")
         if(item._id===id )
           {
            if(item.spaces===1)
             {
              item.spaces=0
              this.cart=this.cart.filter(item=>item._id!==id)
             }
            else 
             item.spaces--;
            //console.log(item.spaces)
          }
          
         })
    toRaw(this.lspaces).forEach(item=>{
         if(item._id===id && item.spaces<item.max)
           {
            item.spaces++;
            //console.log(item.spaces)
          }
         })    
    // else
    //  this.cart=this.cart.filter(item=>item._id!==id)
     
   },
   emptyCart(obj){
          
          this.cart.forEach(item1=>{
              this.cspaces.forEach(item2=>{
                      if(item1._id===item2._id){
                        this.orders.myorders.push({
                        name:obj.myname,
                        phone:obj.phone,
                        lesson_id:item1._id,
                        spaces:item2.spaces
                    }
                    )
                  }
              })
          })
          console.log(toRaw(this.orders))
          
          fetch('http://127.0.0.1:8080/api/order',{
          method: "POST",  
          headers: {
            "Content-Type": "application/json",
          },
          body:JSON.stringify(toRaw(this.orders)) 
        })
        .then(res=>{
          //console.log(res)
          
          this.cart.forEach((item1)=>{
             this.lspaces.forEach((item2)=>{
                 if(item1._id==item2._id){
                    this.updates.push({
                      lesson_id:item1._id,
                      spaces:item2.spaces
                    })
                 }
             })
          })
          //console.log(toRaw(this.updates))
          
          fetch('http://127.0.0.1:8080/api/lessons/update',{
          method: "PUT",  
          headers: {
            "Content-Type": "application/json",
          },
          body:JSON.stringify(toRaw(this.updates)) 
        })
        .then(res=>{
          this.updates=[]
        })
        .catch(err=>console.log(err))

          this.cart=[]
          this.orders.myorders=[]
        })
        .catch(err=>console.log(err))
     
   }
  },
  mounted(){
    fetch('http://127.0.0.1:8080/api/lessons',{
    method: "GET", 
    mode: "cors", 
    cache: "no-cache", 
    credentials: "same-origin", 
    headers: {
      "Content-Type": "application/json",
    },
  })
  .then(data=>data.json())
  .then(res=>{
      
      this.lessons=res
      toRaw(this.lessons).forEach(ele=>{
        this.lspaces.push({_id:ele._id,spaces:ele.spaces,max:ele.spaces})
        this.cspaces.push({_id:ele._id,spaces:0,max:ele.spaces})
       
      })
      //console.log(this.lspaces,this.cspaces)
      this.filter=this.lessons
    })
    .catch(err=>console.log(err))
  },
  computed: {
        sortedLessons() {
          
            if (toRaw(this.lessons).length < 1) return toRaw(this.lessons);

            let sortBy = this.sortby ?? "name";
            let ord = this.orderby ?? "asc";

            return toRaw(this.lessons).sort(function (a, b) {
                let newa = ord === "asc" ? a : b;
                let newb = ord === "asc" ? b : a;
                if (typeof newa[sortBy] === "number") {
                    return (newa[sortBy] - newb[sortBy]);
                } else {
                    return ((newa[sortBy] < newb[sortBy]) ? -1 : ((newa[sortBy] > newb[sortBy]) ? 1 : 0));
                }
            })
           
        },
        doDisable(){
              if(this.cart.length==0)
              return true
              else
              return false
           }
    },
    
  }
</script>