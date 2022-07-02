<template>
    <div>
        <div class="flex">
            <input type="text" placeholder="Name" v-model="order.name" class="w-30 ml-4 bg-gray-100 rounded-full border bg-opacity-50 border-gray-300 focus:ring-2 focus:ring-indigo-200 focus:bg-transparent focus:border-indigo-500 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out">
            <input type="text" placeholder="Phone" v-model="order.phone" class="w-30 ml-4 bg-gray-100 rounded-full border bg-opacity-50 border-gray-300 focus:ring-2 focus:ring-indigo-200 focus:bg-transparent focus:border-indigo-500 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out">
            <button type="button" v-bind:disabled="check()" v-on:click="checkOut()" class=" ml-4 flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Checkout</button>
        </div>
        <div class="p-4 " v-for="lesson in cartItems" :key="lesson.id">
            <div class="flex">
            <img class="lg:h-48 md:h-36" v-bind:src="lesson.image" alt="blog">
            <div class="p-6">
                <h1 class="title-font text-lg font-medium text-gray-900 mb-3" v-text="lesson.title"></h1>
                <h1 class="title-font text-lg font-medium text-gray-900 mb-3" v-text="lesson.location"></h1>
                <h1 class="title-font text-lg font-medium text-gray-900 mb-3" >Number of spaces: {{lesson.space}}</h1>
                <button v-on:click="removeItemFromCart(lesson.id, lesson.spaces)" class="flex mt-6 text-white bg-red-900 border-0 py-2 px-6 focus:outline-none rounded-full">Remove from cart</button>
            </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'checkoutComponent',
    props:{
        cartItems: Array
    },
    data() {
        return {
            order: {
                name: '',
                phone: ''
            },
        }
    },
    methods:{
        removeItemFromCart(id, spaces){
            this.$emit('remove-from-cart', {id: id, spaces: spaces})
        },
        check(){
            if (/^[0-9]+$/.test(this.order.phone) && /^[a-z\s]*$/i.test(this.order.name)){
                return false
            }
            return true
        },
        checkOut(){
            this.$emit('checkout', this.order)
            this.clearCheckoutForm()
        },
        clearCheckoutForm: function(){
            this.order.name = ""
            this.order.phone = ""
        },
    }
}
</script>