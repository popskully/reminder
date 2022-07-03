<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
// const input_category = ref(null);

// sort todos in descending order (last is displayed first)
const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

// Add todos to array
const addTodo = () => {
  if (
    input_content.value.trim() === ""
    // || input_category.value === null
  ) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    // category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  // input_category.value = null;
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
  <main class="app bg-gray-50 mx-auto lg:px-72 px-5 container">
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

    <section class="">
      <h4 class="text-lg text-gray-600 font-semibold mb-5">
        What's on your list?
      </h4>
      <form
        @submit.prevent="addTodo"
        class="flex rounded-lg mb-5 gap-2 justify-center"
      >
        <input
          class="w-5/6 h-14 p-3 rounded-lg font-medium text-gray-500 outline-none border-gray-300 border-2 bg-transparent focus:border-gray-500 hover:border-gray-500 ease-in-out duration-200"
          type="text"
          placeholder="e.g. Buy some eggs"
          v-model="input_content"
        />
        <!-- <h4 class="mt-5 text-base text-gray-600 font-medium">
          Pick a category
        </h4> -->

        <!-- <div class="grid grid-cols-2 gap-4 w-full mt-5">
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
        </div> -->

        <!-- <input
          class="w-1/4 bg-blue-500 rounded-lg text-white font-medium cursor-pointer hover:opacity-80 transition ease-in-out duration-200"
          type="submit"
          value="Add"
        /> -->
        <button
          @click="addTodo"
          class="bg-blue-500 hover:opacity-80 hover:transition hover:ease-in-out hover:duration-200 w-1/6 rounded-lg text-white"
        >
          <svg
            class="mx-auto hover:rotate-45 hover:transition hover:ease-in-out hover:duration-200"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            fill="currentColor"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              fill-rule="evenodd"
              clip-rule="evenodd"
              d="M21.1568 4.15386C21.5068 3.32665 20.6734 2.49328 19.8462 2.84325L3.46966 9.77178C2.60871 10.136 2.67381 11.3772 3.56814 11.6494L8.7707 13.2328C8.9155 13.2769 9.05556 13.3314 9.18998 13.3956L14.2927 8.29291C14.6832 7.90239 15.3164 7.90239 15.7069 8.29291C16.0974 8.68344 16.0974 9.3166 15.7069 9.70712L10.6043 14.8098C10.6685 14.9443 10.7231 15.0844 10.7672 15.2293L12.3506 20.4319C12.6228 21.3262 13.864 21.3913 14.2283 20.5304L21.1568 4.15386ZM19.0669 1.00131C21.5485 -0.0486059 24.0486 2.45151 22.9987 4.93314L16.0702 21.3097C14.9775 23.8925 11.2538 23.6972 10.4373 21.0142L8.85389 15.8117C8.80534 15.6522 8.71878 15.5102 8.60426 15.3957C8.48979 15.2812 8.34782 15.1947 8.18837 15.1462L2.98581 13.5628C0.302836 12.7462 0.107539 9.02259 2.69038 7.92985L19.0669 1.00131Z"
              fill="currentColor"
            />
          </svg>
        </button>
      </form>
    </section>
    <section class="todo-list">
      <h3 class="text-base text-gray-600 font-semibold mb-5">
        Task List ({{ todos.length }})
      </h3>
      <span
        v-if="todos.length < 1"
        class="bg-white opacity-80 drop-shadow-sm flex flex-col w-full p-5 rounded-lg text-lg text-slate-900 font-medium"
        ><p class="text-center mx-auto">Sorry, you have no task listed</p>
        <img class="w-14 h-14 mx-auto" src="./sad.gif" alt="" />
      </span>
      <div v-else class="transition duration-200 ease-in-out" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :class="`todo-item flex items-center bg-white p-4 rounded-lg drop-shadow-sm mb-4 ${
            todo.done && 'done'
          }`"
        >
          <label class="mr-4 cursor-pointer">
            <input type="checkbox" v-model="todo.done" />
            <span class="bubble"></span>
            <!-- <span
              :class="`bubble 
              ${todo.category == 'business' ? 'business' : 'personal'}`"
            ></span> -->
          </label>

          <div
            class="shrink grow basis-[0%] todo-content text-gray-600 font-medium"
          >
            <input
              class="w-full outline-none"
              type="text"
              v-model="todo.content"
            />
          </div>

          <div>
            <button
              class="bg-red-500 hover:opacity-80 ease-in-out duration-200 text-white font-medium text-sm p-2 rounded"
              @click="removeTodo(todo)"
            >
              <svg
                x="0px"
                y="0px"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                fill="currentColor"
              >
                <path
                  d="M 10 2 L 9 3 L 4 3 L 4 5 L 5 5 L 5 20 C 5 20.522222 5.1913289 21.05461 5.5683594 21.431641 C 5.9453899 21.808671 6.4777778 22 7 22 L 17 22 C 17.522222 22 18.05461 21.808671 18.431641 21.431641 C 18.808671 21.05461 19 20.522222 19 20 L 19 5 L 20 5 L 20 3 L 15 3 L 14 2 L 10 2 z M 7 5 L 17 5 L 17 20 L 7 20 L 7 5 z M 9 7 L 9 18 L 11 18 L 11 7 L 9 7 z M 13 7 L 13 18 L 15 18 L 15 7 L 13 7 z"
                ></path>
              </svg>
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

:root {
  /* --primary: #ea40a4; */
  --business: rgb(59 130 246);
  /* --personal: var(--primary); */
}

input[type="radio"],
input[type="checkbox"] {
  display: none;
}

.bubble {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border-radius: 25%;
  border: 2px solid var(--business);
}

/* .bubble.personal {
  border-color: var(--personal);
} */

.bubble::after {
  content: "";
  display: block;
  opacity: 0;
  width: 0px;
  height: 0px;
  background-color: var(--business);
  border-radius: 25%;
  transition: 0.2s ease-in-out;
}

input:checked ~ .bubble::after {
  width: 10px;
  height: 10px;
  opacity: 1;
}

.todo-item.done .todo-content input {
  text-decoration: line-through;
  transition: 0.2s ease-in-out;
}
</style>
