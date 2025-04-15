<template>
<main class="min-h-screen bg-gradient-to-br from-gray-100 to-blue-50 text-gray-800 p-6 max-w-2xl mx-auto space-y-8">

    <section class="text-center">
        <h2 class="text-3xl font-bold mb-2">
            What's up, <input type="text" id="name" placeholder="Name here" v-model="name" autocomplete="off" class="inline-block text-blue-500 bg-transparent border-b-2 border-blue-400 focus:outline-none focus:border-blue-600 px-2 py-1">
        </h2>
    </section>

    <section class="bg-white shadow rounded-lg p-6 space-y-4">
        <h3 class="text-xl font-semibold text-blue-600">CREATE A TODO</h3>

        <form @submit.prevent="addTodo" class="space-y-4">
            <h4 class="block text-sm font-medium text-gray-700 mb-1">{{ name ? `${name}, what's on your todo list?` : "What's on your todo list?" }}</h4>
            <input type="text" name="content" autocomplete="off" id="content" placeholder="e.g. make a video" v-model="input_content" class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400" />

            <div>
                <label class="block text-sm font-medium text-gray-700 mb-1">Pick a category</label>
                <div class="flex gap-4">
                    <label class="flex items-center gap-2 cursor-pointer">
                        <input type="radio" value="business" v-model="input_category" />
                        <span class="text-sm font-medium">Business</span>
                    </label>
                    <label class="flex items-center gap-2 cursor-pointer">
                        <input type="radio" value="personal" v-model="input_category" />
                        <span class="text-sm font-medium">Personal</span>
                    </label>
                </div>
            </div>

            <input type="submit" value="Add todo" class="bg-blue-500 hover:bg-blue-600 text-white font-semibold px-4 py-2 rounded cursor-pointer transition" />
        </form>
    </section>

    <section>
        <h3 class="font-bold pb-4">TODO LIST</h3>
        <transition-group name="todo" tag="div">
            <div v-for="todo in todos" :key="todo.id" class="flex items-center justify-between bg-white shadow p-4 mb-2 rounded">
                <div>
                    <p class="font-medium">{{ todo.content }}</p>
                    <p :class="todo.category === 'business' ? 'text-blue-500' : 'text-pink-500'">{{ todo.category }}</p>
                </div>
                <button @click="removeTodo(todo)" class="text-red-500 hover:text-red-700">Delete</button>
            </div>
        </transition-group>
    </section>

</main>
</template>

<script setup>
import {
    ref,
    watch,
    onMounted
} from 'vue'

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
    if (input_content.value.trim() === '' || input_category.value === null) {
        return
    }
    todos.value.push({
        id: Date.now(),
        content: input_content.value,
        category: input_category.value,
        done: false
    })

    input_category.value = null
    input_content.value = ''
}

const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo)
}
</script>

<style scoped>
.todo-enter-active,
.todo-leave-active {
    transition: all 0.3s ease;
}

.todo-enter-from,
.todo-leave-to {
    opacity: 0;
    transform: translateY(-10px);
}
</style>
