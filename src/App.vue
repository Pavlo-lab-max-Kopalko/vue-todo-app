<script setup>
  import { ref, computed, onBeforeMount, watch } from 'vue';
  import originalTodos from './data/todos';

  // let initialTodos = [];

  // try {
  //   initialTodos = JSON.parse(localStorage.getItem('todos'));
  // } catch (error) {}

  // if (!Array.isArray(initialTodos)) {
  //   initialTodos = [];
  // }

  const todos = ref([]);

  onBeforeMount(() => {
    try {
      todos.value = JSON.parse(localStorage.getItem('todos'));
    } catch (error) {}

    if (!Array.isArray(todos.value)) {
      todos.value = [];
    }
  });

  watch(
    todos,
    newTodos => {
      localStorage.setItem('todos', JSON.stringify(newTodos));
    },
    { deep: true },
  );

  const title = ref("");
  const activeTodos = computed(() =>
    todos.value.filter((todo) => !todo.completed)
  );

  const status = ref('all');

  const visibleTodos = computed(() => {
    if (status.value === 'active') {
      return activeTodos.value;
    }

    if (status.value === 'completed') {
      return todos.value.filter(todo => todo.completed);
    }

    return todos.value;
  });

  function addTodo() {
    if (!title.value) {
      errorMessage.value = "Title should not be empty";

      return;
    };

    todos.value.push({
      id: Date.now(),
      title: title.value,
      completed: false,
    });

    title.value = "";
  }

  const errorMessage = ref("");

</script>

<template>
  
  <form @submit.prevent="addTodo">
    
    <input
      class="todoapp__new-todo"
      placeholder="What needs to be done?"
      @input="errorMessage = ''"
      v-model="title"
    />
  </form>

  <div
    class="todo"
    v-for="(todo, i) of visibleTodos"
    :key="todo.id"
    :class="{ completed: todo.completed }"
  >
    <label class="todo__status-label">
      <input
        type="checkbox"
        class="todo__status"
        v-model="todo.completed"
      />
    </label>

    <form v-if="false">
      <input 
        type="text" 
        class="todo__title-field" 
        placeholder="Empty todo will be deleted" 
      />
    </form>

    <template v-else>
      <span class="todo__title">{{ todo.title }}</span>
      <button class="todo__remove" @click="todos.splice(i, 1)">×</button>
    </template>

    <div class="modal overlay" :class="{ 'is-active': false }">
      <div class="modal-background has-background-white-ter"></div>
      <div class="loader"></div>
    </div>
  </div>

  <button
    class="todoapp__clear-completed"
    :disabled="todos.length === activeTodos.length"
    @click="todos = activeTodos"
  >
    Clear completed
  </button>

  <nav class="filter">
    <a
      href="#/"
      class="filter__link"
      :class="{ selected: status === 'all' }"
      @click="status = 'all'"
    >
      All
    </a>
    <a
      href="#/active"
      class="filter__link"
      :class="{ selected: status === 'active' }"
      @click="status = 'active'"
    >
      Active
    </a>
    <a
      href="#/completed"
      class="filter__link"
      :class="{ selected: status === 'completed' }"
      @click="status = 'completed'"
    >
      Completed
    </a>
  </nav>

  <div
    class="notification is-danger is-light has-text-weight-normal" 
    v-if="errorMessage"
  >
    <button 
      class="delete"
      @click="errorMessage = ''"
    ></button>
    {{ errorMessage }}
  </div>

</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
