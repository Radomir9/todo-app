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
                <input type="text" autocomplete="off" v-model="input_content" ref="todoInput" @keyup.enter="addTodo"
                    placeholder="Task content"
                    class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400" />

                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Pick a priority</label>
                    <div class="flex gap-4">
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="radio" value="low" v-model="input_priority" />
                            <span class="text-sm font-medium">Low</span>
                        </label>
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="radio" value="medium" v-model="input_priority" />
                            <span class="text-sm font-medium">Medium</span>
                        </label>
                        <label class="flex items-center gap-2 cursor-pointer">
                            <input type="radio" value="high" v-model="input_priority" />
                            <span class="text-sm font-medium">High</span>
                        </label>
                    </div>
                </div>
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

                <div class="flex items-center justify-between mt-4">
                    <input type="submit" value="Add todo"
                        class="bg-blue-500 hover:bg-blue-600 text-white font-semibold px-4 py-2 rounded cursor-pointer transition" />
                    <button @click="clearCompleted" type="button"
                        class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600 transition cursor-pointer">
                        Clear Completed Tasks
                    </button>
                </div>

            </form>

        </section>

        <section>
            <h3 class="font-bold pb-4">TODO LIST</h3>
            <div class="mb-4">
                <label for="filter" class="text-sm font-medium text-gray-700">Filter tasks</label>
                <select v-model="filter" id="filter"
                    class="w-full border border-gray-300 rounded px-3 py-2 mt-2 focus:outline-none focus:ring-2 focus:ring-blue-400">
                    <option disabled value="">-- Filter tasks --</option>
                    <option value="all">All</option>
                    <option value="done">Completed</option>
                    <option value="notDone">Not completed</option>
                    <option value="business">Business</option>
                    <option value="personal">Personal</option>
                </select>
            </div>
            <transition-group name="todo" tag="div">
                <div v-for="todo in sortedAndFilteredTodos" :key="todo.id" :class="[
                    'flex items-center justify-between bg-white shadow-sm p-4 mb-2 rounded-xl transition-transform transform hover:scale-[1.01]',
                    todo.done && 'opacity-50'
                ]">
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
                        <div class="flex flex-col gap-1">
                            <div class="flex items-center gap-2">
                                <div class="flex items-center gap-2">
                                    <p :class="todo.done ? 'line-through text-gray-400' : 'text-gray-800'"
                                        class="font-semibold text-base">
                                        {{ todo.content }}
                                    </p>
                                    <span :class="[
                                        'text-xs font-semibold px-2 py-0.5 rounded-full capitalize',
                                        todo.category === 'business' && 'bg-blue-100 text-blue-600',
                                        todo.category === 'personal' && 'bg-pink-100 text-pink-600'
                                    ]">
                                        {{ todo.category }}
                                    </span>
                                </div>
                                <span :class="[
                                    'flex items-center gap-1 text-xs font-semibold px-2 py-0.5 rounded-full capitalize',
                                    todo.priority === 'low' && 'bg-green-100 text-green-600',
                                    todo.priority === 'medium' && 'bg-yellow-100 text-yellow-600',
                                    todo.priority === 'high' && 'bg-red-100 text-red-600'
                                ]">
                                    <svg v-if="todo.priority === 'low'" xmlns="http://www.w3.org/2000/svg"
                                        class="h-3 w-3" fill="currentColor" viewBox="0 0 20 20">
                                        <path d="M10 2a8 8 0 100 16 8 8 0 000-16z" />
                                    </svg>
                                    <svg v-else-if="todo.priority === 'medium'" xmlns="http://www.w3.org/2000/svg"
                                        class="h-3 w-3" fill="currentColor" viewBox="0 0 20 20">
                                        <path d="M10 2a8 8 0 100 16 8 8 0 000-16z" />
                                    </svg>
                                    <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" fill="currentColor"
                                        viewBox="0 0 20 20">
                                        <path fill-rule="evenodd"
                                            d="M10 2a8 8 0 11-6.472 12.972l-.528.528a1 1 0 01-1.414-1.414l.528-.528A8 8 0 0110 2zm0 5a1 1 0 00-.993.883L9 8v4a1 1 0 001.993.117L11 12V8a1 1 0 00-1-1zm0 8a1 1 0 100 2 1 1 0 000-2z"
                                            clip-rule="evenodd" />
                                    </svg>
                                    {{ todo.priority }}
                                </span>
                            </div>
                            <p class="text-xs text-gray-400">
                                Added: {{ new Date(todo.createdAt).toLocaleString() }}
                            </p>

                        </div>
                    </div>
                    <button @click="removeTodo(todo)"
                        class="text-gray-400 hover:text-red-500 transition ml-4 cursor-pointer">
                        🗑️
                    </button>
                </div>

            </transition-group>
        </section>
        <div class="text-sm text-gray-600 mb-4">
            Completed: {{todos.filter(todo => todo.done).length}} / {{ todos.length }} tasks
        </div>
    </main>
</template>

<script setup>
import { ref, watch, onMounted, computed, nextTick } from "vue";

const name = ref("");
const todos = ref([]);

const input_category = ref(null);
const input_content = ref("");

const input_priority = ref('medium')

const todoInput = ref(null);

const filter = ref('all')



const sortedAndFilteredTodos = computed(() => {
    const filtered = (() => {
        if (filter.value === 'all') return todos.value;
        if (filter.value === 'done') return todos.value.filter(todo => todo.done);
        if (filter.value === 'notDone') return todos.value.filter(todo => !todo.done);
        return todos.value.filter(todo => todo.category === filter.value);
    })();

    const priorities = ['high', 'medium', 'low'];
    return filtered.sort((a, b) => priorities.indexOf(a.priority) - priorities.indexOf(b.priority));
});

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
    nextTick(() => {
        todoInput.value?.focus()
    });
});

const addTodo = () => {
    if (input_content.value.trim() === "" || input_category.value === null) {
        alert("Please enter content and choose a category.")
        return;
    }
    todos.value.push({
        id: Date.now(),
        content: input_content.value,
        category: input_category.value,
        done: false,
        priority: input_priority.value,
        createdAt: new Date().toISOString()
    });

    input_category.value = null;
    input_content.value = "";
    input_priority.value = "medium"
};



const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo);
};

const clearCompleted = () => {
    todos.value = todos.value.filter(todo => !todo.done)
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
