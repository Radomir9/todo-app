<template>
  <main>

    <section class="greeting">
      <h2>
        What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
      
    </section>

    <section>
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo"> 
        <h4>What's on your todo list?</h4>
        <input type="text" name="content" id="content" placeholder="e.g. make a video" v-model="input_content"/>

        <h4>Pick a category</h4>
        <div>

          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <!-- <span></span> -->
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <!-- <span></span> -->
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" />
      </form>
    </section>

    <section>
      <h3>TODO LIST</h3>
      <div v-for="todo in todos">
        <div>
          {{ todo.content }}
        </div>
        <button @click="removeToDo(todo)">Delete</button>
      </div>
    </section>

  </main>
</template>


<script setup>
import { ref, watch, onMounted } from 'vue'

const name = ref('')
const todos = ref([])

const input_category = ref(null)
const input_content = ref('')


watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
}) 

const addTodo = () => {
  if(input_content.value.trim() === '' || input_category.value === null){
    return 
  } 
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false
  })

  input_category.value = null
  input_content.value = ''
}

const removeToDo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}
</script>

<style scoped></style>