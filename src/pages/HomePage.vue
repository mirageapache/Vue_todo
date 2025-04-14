<script setup lang="ts">
  import { onMounted, reactive, ref } from 'vue';
  import FunctionalBar from '../components/FunctionalBar.vue';
  import ListPanel from '../components/ListPanel.vue';
  import type { TodoListType } from '../types/todoList';

  const todoList = reactive<TodoListType[]>([]);
  const isHideCompleted = ref<boolean>(false);
  
  onMounted(() => {
    const localList = localStorage.getItem('todoList');
    if (localList) {
      todoList.push(...JSON.parse(localList));
    }
  })

  /** 顯示/隱藏已完成項目 */
  const hideCompleted = () => {
    isHideCompleted.value = !isHideCompleted.value;
  }

  /** 更新 todoList */
  const updateTodoList = (newList: TodoListType[]) => {
    todoList.splice(0, todoList.length, ...newList);
    localStorage.setItem('todoList', JSON.stringify(todoList));
  }
</script>

<template>
  <div class="homePage">
    <h1 class="banner">Todo List</h1>
    <div class="todoList">
      <FunctionalBar :todoList="todoList" :isHideCompleted="isHideCompleted" :hideCompleted="hideCompleted" />
      <ListPanel :todoList="todoList" @update:todoList="updateTodoList" />
    </div>
  </div>  
</template>

<style scoped>
.homePage {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.banner {
  text-align: center;
  margin: 20px 0;
}

.todoList {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
}

</style>

