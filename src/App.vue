<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

// sort todos in descending order (last is displayed first)
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

// Add todos to array
const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = null;
};

// remove todos from array
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

// save name to local storage
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app bg-gray-50 px-5">
    <section class="sticky bg-gray-50 top-0 py-10 w-full mt-0 flex">
      <h2 class="text-2xl max-w-full font-bold text-gray-700">
        Hi there,
        <input
          class="bg-transparent focus:outline-none"
          type="text"
          placeholder="Name here"
          v-model="name"
        />
      </h2>
    </section>

    <section class="create-todo">
      <form @submit.prevent="addTodo">
        <h4 class="text-lg text-gray-600 font-semibold mb-5">
          What's on your list?
        </h4>
        <input
          class="p-3 rounded-lg focus:outline-2 outline-gray-300 w-full border-2 bg-transparent"
          type="text"
          placeholder="e.g. Buy some eggs"
          v-model="input_content"
        />
        <h4 class="mt-5 text-base text-gray-600 font-medium">
          Pick a category
        </h4>

        <div class="grid grid-cols-2 gap-4 w-full mt-5">
          <label
            class="bg-white border-2 space-y-3 text-center p-5 rounded-lg font-medium text-gray-600 text-xl"
          >
            <input
              class="w-4 h-4"
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="w-10 h-10"></span>
            <div>Business</div>
          </label>
          <label
            class="bg-white border-2 space-y-3 text-center p-5 rounded-lg font-medium text-gray-600 text-xl"
          >
            <input
              class="w-4 h-4"
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input
          class="w-full p-5 bg-blue-500 my-5 rounded-lg text-white font-medium"
          type="submit"
          value="Add Todo"
        />
      </form>
    </section>
    <section class="todo-list">
      <h3 class="text-base text-gray-600 font-medium">Todo List</h3>
      <div class="my-5">
        <div
          v-for="todo in todos_asc"
          :class="`flex items-center  bg-white border-2 p-4 mb-4 rounded-lg ${
            todo.done && 'line-through'
          }`"
        >
          <label class="mr-4 cursor-pointer">
            <input
              class="w-4 h-4 rounded-full"
              type="checkbox"
              v-model="todo.done"
            />
            <span :class="`line-through ${todo.category}`"></span>
          </label>

          <div class="grow shrink basis-[0%]">
            <input
              class="w-full font-semibold text-gray-700 outline-none"
              type="text"
              v-model="todo.content"
            />
          </div>
          <div class="actions">
            <button
              class="bg-red-500 text-white hover:opacity-80 font-medium px-4 py-2 rounded-lg"
              @click="removeTodo(todo)"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style>
body {
  background-color: rgb(249 250 251);
}
</style>
