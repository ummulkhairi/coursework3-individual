<template>
  <div class="p-4 ">
    <div class="flex">
      <img class="lg:h-48 md:h-36" v-bind:src="lesson.image" alt="blog">
      <div class="p-6">               
          <h1 class="title-font text-lg font-medium text-gray-900 mb-3" v-text="lesson.title"></h1>
          <h1 class="title-font text-lg font-medium text-gray-900 mb-3" v-text="lesson.location"></h1>
          <h1 class="title-font text-lg font-medium text-gray-900 mb-3" >Price:${{lesson.price}}</h1>
          <h1 class="title-font text-lg font-medium text-gray-900 mb-3" >Number of spaces: {{lesson.space}}</h1>
          <button v-on:click="addToCart" v-bind:disabled="disableAddToCart" class="flex mt-6 text-white bg-blue-500 border-0 py-2 px-6 focus:outline-none  rounded-full">Add to cart</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'lessonComponent',
  props:{
    lesson: Object
  },
  computed:{
    disableAddToCart(){
        if (this.lesson.space >= 1){
            return false
        }
        return true
    },
  },
  methods:{
      addToCart(){
          this.$emit('add-to-cart', this.lesson)
      }
  },
}
</script>
