<script setup lang="ts">
  import { onMounted, reactive, ref } from 'vue';
  const todoList = reactive<string[]>([]);
  const todo = ref<string>('');
  
  onMounted(() => {
    const localList = localStorage.getItem('todoList');
    if (localList) {
      todoList.push(...JSON.parse(localList));
    }
  })

  /** 新增項目 */
  const addTodo = () => {
    todoList.push(todo.value);
    todo.value = '';
    localStorage.setItem('todoList', JSON.stringify(todoList));
  };
</script>

<template>
  <div class="homePage">
    <h1>Todo List</h1>
    <div class="todoList">
      <div class="todoItem">
        <input type="text" v-model="todo" placeholder="Add a todo" />
        <button @click="addTodo">Add</button>
      </div>
      <div class="todoItem">
        <p v-if="todoList.length === 0">No todo items</p>
        <p v-else v-for="item in todoList">{{ item }}</p>
      </div>
    </div>
  </div>  
</template>

<style scoped>

</style>

