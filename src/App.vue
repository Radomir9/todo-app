<template>
    <main class="min-h-screen bg-gradient-to-br from-gray-100 to-blue-50 text-gray-800 p-6 max-w-2xl mx-auto space-y-8">
        <section class="text-center">
            <h2 class="text-3xl font-bold mb-2">
                What's up,
                <input type="text" id="name" placeholder="Name here" v-model="name" autocomplete="off"
                    class="inline-block text-blue-500 bg-transparent border-b-2 border-blue-400 focus:outline-none focus:border-blue-600 px-2 py-1" />
            </h2>
        </section>

        <section class="bg-white shadow rounded-lg p-6 space-y-4">
            <h3 class="text-xl font-semibold text-blue-600">CREATE A TODO</h3>

            <form @submit.prevent="addTodo" class="space-y-4">
                <h4 class="block text-sm font-medium text-gray-700 mb-1">
                    {{
                        name
                            ? `${name}, what's on your todo list?`
                            : "What's on your todo list?"
                    }}
                </h4>
                <input type="text" name="content" autocomplete="off" id="content" placeholder="e.g. make a video"
                    v-model="input_content"
                    class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400" />

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

                <input type="submit" value="Add todo"
                    class="bg-blue-500 hover:bg-blue-600 text-white font-semibold px-4 py-2 rounded cursor-pointer transition" />
            </form>
        </section>

        <section>
            <h3 class="font-bold pb-4">TODO LIST</h3>
            <div class="mb-4">
                <label for="filter" class="text-sm font-medium text-gray-700">Filter tasks</label>
                <select v-model="filter" id="filter"
                    class="w-full border border-gray-300 rounded px-3 py-2 mt-2 focus:outline-none focus:ring-2 focus:ring-blue-400">
                    <option value="all">All</option>
                    <option value="done">Completed</option>
                    <option value="notDone">Not completed</option>
                    <option value="business">Business</option>
                    <option value="personal">Personal</option>
                </select>
            </div>
            <transition-group name="todo" tag="div">
                <div v-for="todo in filteredTodos" :key="todo.id"
                    class="flex items-center justify-between bg-white shadow-sm p-4 mb-2 rounded-xl hover:shadow-md transition">
                    <div class="flex items-start gap-4 w-full">
                        <button @click="todo.done = !todo.done"
                            class="w-6 h-6 flex items-center justify-center rounded-full border-2 transition-all duration-200"
                            :class="todo.done ? 'bg-blue-500 border-blue-500' : 'border-gray-300'
                                ">
                            <svg v-if="todo.done" class="h-4 w-4 text-white" fill="none" viewBox="0 0 24 24"
                                stroke="currentColor" stroke-width="3">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                            </svg>
                        </button>
                        <div class="flex flex-col">
                            <p :class="todo.done ? 'line-through text-gray-400' : 'text-gray-800'
                                " class="font-semibold text-base">
                                {{ todo.content }}
                            </p>
                            <p :class="todo.category === 'business'
                                    ? 'text-blue-500'
                                    : 'text-pink-500'
                                " class="text-sm mt-1">
                                {{ todo.category }}
                            </p>
                        </div>
                    </div>
                    <button @click="removeTodo(todo)"
                        class="text-gray-400 hover:text-red-500 transition ml-4 cursor-pointer">
                        🗑️
                    </button>
                </div>
                
            </transition-group>
            <p class="text-sm text-gray-500 mt-4">
                     Total: {{ todos.length }} tasks
                 </p>
        </section>
    </main>
</template>

<script setup>
import { ref, watch, onMounted, computed } from "vue";

const name = ref("");
const todos = ref([]);

const input_category = ref(null);
const input_content = ref("");

const filter = ref('all')

const filteredTodos = computed(() => {
    if(filter.value === 'all'){
        return todos.value
    }
    if(filter.value === 'done') {
        return todos.value.filter(todo => todo.done)
    }
    if(filter.value === 'notDone') {
        return todos.value.filter(todo => !todo.done)
    }
    return todos.value.filter(todo => todo.category === filter.value)
})

watch(name, (newVal) => {
    localStorage.setItem("name", newVal);
});

watch(
    todos,
    (newVal) => {
        localStorage.setItem("todos", JSON.stringify(newVal));
    },
    {
        deep: true,
    }
);

onMounted(() => {
    name.value = localStorage.getItem("name") || "";
    todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

const addTodo = () => {
    if (input_content.value.trim() === "" || input_category.value === null) {
        return;
    }
    todos.value.push({
        id: Date.now(),
        content: input_content.value,
        category: input_category.value,
        done: false,
    });

    input_category.value = null;
    input_content.value = "";
};

const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo);
};
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
