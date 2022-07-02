<template>
  <div id="app">
    <div class="flex justify-between">
        <h1 class="title-font text-2xl font-bold text-blue-800 pl-8 pt-8" v-text="sitename"></h1>
        <div class="p-8 flex">
            <select v-on:change="reorderList" v-model="items_order" class="w-30 ml-4 bg-gray-100 rounded-full border bg-opacity-50 border-gray-300 focus:ring-2 focus:ring-indigo-200 focus:bg-transparent focus:border-indigo-500 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out">
                <option value="">-- Sort order --</option>
                <option value="ascending">Ascending</option>
                <option value="descending">Descending</option>
            </select>
            <button v-on:click="sortSubject"  class="flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Subject</button>
            <button v-on:click="sortPrice"  class="flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Price</button>
            <button v-on:click="sortAvailable"  class="flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Availability</button>
            <button v-on:click="sortLocation"  class="flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Location</button>
            <input v-on:input="fetchFlteredLessons" type="text" placeholder="Search" v-model="userInput"  class="w-30 ml-4 bg-gray-100 rounded-full border bg-opacity-50 border-gray-300 focus:ring-2 focus:ring-indigo-200 focus:bg-transparent focus:border-indigo-500 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out">

        </div>
        <div class="mr-8">
            <button v-on:click="displayCart" v-if="canDisplayCart" class="flex mt-6 text-white bg-black border-0 py-2 px-6 focus:outline-none hover:bg-black rounded-full "> <span class="fa fa-shopping-cart mt-1 mr-2"></span>Cart {{cartcount}}</button>
            <button v-else disabled  class="flex mt-6 text-white bg-black opacity-25 border-0 py-2 px-6 focus:outline-none hover:bg-black rounded-full " ><span class="fa fa-shopping-cart mt-1 mr-2"></span>Cart</button>
        </div>
    </div>
    <section class="text-gray-600 body-font">
        <div class="flex flex-wrap -m-4" v-if="display">
            <div v-for="lesson in lessons" :key="lesson.id">
                <lesson-component :lesson="lesson" v-on:add-to-cart="addCart($event)"></lesson-component>
            </div>
        </div>
        <div v-else>
            <checkout-component v-on:checkout="checkout($event)" :cart-items=" getCartItemsInfo()" v-on:remove-from-cart="removeItemFromCart($event)"></checkout-component>
        </div>
    </section>
  </div>
</template>

<script>
import lessonComponent from './components/lessonComponent.vue'
import checkoutComponent from './components/checkoutComponent.vue'
export default {
  name: 'App',
  components: {
    lessonComponent,
    checkoutComponent
  },
  data() {
    return {
        sitename:'After School Club',
            lessons:[],
            phone:"",
            name:"",
            userInput:'',
            items_order: '',
            cart:[],
            display:true,
    }
  },
  created() {
    this.fetchLessons()
  },
  computed: {
    canDisplayCart(){
            return this.cart.length > 0 ? true : false
        },
    cartcount(){
        return this.cart.length
    }
    },
  methods: {
     addCart(lesson){
        if (lesson.space >= 1) {
        let ifLessonInCart = false
            if (this.cart.length >= 1){
                for (let i = 0; i < this.cart.length; i++) {
                    if (this.cart[i].id == lesson.id){
                        this.cart[i].space += 1
                        ifLessonInCart = true
                        break
                    }
                }
                if (ifLessonInCart == false) {
                    let item = {}
                    item.id = lesson.id
                    item.space = 1
                    this.cart.push(item)
                }
            }
            else{
                let item = {}
                item.id = lesson.id
                item.space = 1
                this.cart.push(item)
            }
            lesson.space -= 1
        }
        else{
            lesson.space = 0
        }
    },
    getCartItemsInfo: function(){
        let show_cart_items = []
        for (let i = 0; i < this.cart.length; i++) {
            for (let j = 0; j < this.lessons.length; j++) {
                if (this.lessons[j].id == this.cart[i].id) {
                    let item = {}
                    item.id = this.cart[i].id
                    item.title = this.lessons[j].title
                    item.location = this.lessons[j].location
                    item.image = this.lessons[j].image
                    item.space = this.cart[i].space
                    show_cart_items.push(item)
                }   
            }
        }
        return show_cart_items
    },
    removeItemFromCart: function(obj){
        for (let i = 0; i < this.lessons.length; i++) {
            if (this.lessons[i].id == obj.id) {
                this.lessons[i].space += obj.space
            }
        }
        for (let i = 0; i < this.cart.length; i++) {
            if (this.cart[i].id == obj.id) {
                this.cart.splice(i, 1)
            }
        }
    },
    displayCart(){
        this.display = !this.display
    },
    fetchLessons(){
        fetch("https://umu-cst2-individual.herokuapp.com/collection/lesson")
            .then(res => {
                return res.json()
            })
            .then(data => {
                this.lessons = data
            })
            .catch(err => {
                this.lessons = []
                console.log("unable to get lessons", err)
            })
    },
    reorderList(){
        if (this.items_order == "descending"){
            this.lessons.reverse()
        }
    },
    fetchFlteredLessons(){
        console.log(this.userInput)
        fetch(`https://umu-cst2-individual.herokuapp.com/collection/lesson/search?search=${this.userInput}`)
        .then(res => {
            return res.json()
        })
        .then(data => {
            console.log(data)
            this.lessons = data
        })
        .catch(err => {
            this.lessons = []
            console.log(`unable to get lessons: ${err}`)
        })
    },
    canCart:function(lesson){
        return lesson.availableSpace > this.cartCount(lesson.id)
    },
    cartCount(lessonId){
        let count = 0;
        for (let index = 0; index < this.cart.length; index++) {
            if(lessonId === this.cart[index]){
                count ++;
            }
        }
        return count;
    } ,
    checkout(userObj){
        let order = {
            name: userObj.name,
            phone: userObj.phone,
            items: this.cart
        }
        let order_str = (JSON.stringify(order))
        fetch('https://umu-cst2-individual.herokuapp.com/collection/orders', {
            method: "POST",
            body: order_str,
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
        .then(response => response.json())
        .then(json_response => {
            console.log(json_response)
            this.updateSpacesInDb()
        })
        .catch(err => console.log(err))
    },
    updateSpacesInDb: function(){
        let spaces_upd = []
        for (let i = 0; i < this.cart.length; i++) {
            for (let j = 0; j < this.lessons.length; j++) {
                if (this.cart[i].id == this.lessons[j].id) {
                    let item = {
                        id: this.cart[i].id,
                        space: this.lessons[j].space
                    }
                    spaces_upd.push(item)
                }
            }    
        }
        let spaces_upd_str = (JSON.stringify(spaces_upd ))
        fetch('https://umu-cst2-individual.herokuapp.com/collection/lesson', {
            method: "PUT",
            body: spaces_upd_str,
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
        .then(response => response.json())
        .then(response => {
            console.log(response)
            this.cart = []
            alert('Your order has been submitted')
            this.display = true;
        })
        .catch(err => console.log(err))
    },
    sortSubject(){
        this.lessons.sort((obj1,obj2)=>{
            if(obj1.title == obj2.title) return 0;
            if(obj1.title < obj2.title) return -1;
            return 1;
        });
    },
    sortLocation(){
        this.lessons.sort((obj1,obj2)=>{
            if(obj1.location == obj2.location) return 0;
            if(obj1.location < obj2.location) return -1;
            return 1;
        });
    },
    sortPrice(){
        this.lessons.sort((obj1,obj2)=>{
            if(obj1.price == obj2.price) return 0;
            if(obj1.price < obj2.price) return -1;
            return 1;
        });
    },
    sortAvailable(){
        this.lessons.sort((obj1,obj2)=>{
            if(obj1.space == obj2.space) return 0;
            if(obj1.space < obj2.space) return -1;
            return 1;
        });
    },
  }
}
</script>