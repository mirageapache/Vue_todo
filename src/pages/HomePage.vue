<script setup lang="ts">
import { onMounted, ref, watch } from "vue";
import FunctionalBar from "../components/FunctionalBar.vue";
import ListPanel from "../components/ListPanel.vue";
import type { TodoListType } from "../types/todoList";

const todoList = ref<TodoListType[]>([]);
const isHideCompleted = ref<boolean>(true);

// Load from localStorage
onMounted(() => {
  const localList = localStorage.getItem("todoList");
  if (localList) {
    todoList.value = JSON.parse(localList);
  }
});

// Save to localStorage whenever todoList changes
watch(
  todoList,
  (newVal) => {
    localStorage.setItem("todoList", JSON.stringify(newVal));
  },
  { deep: true }
);

/** 顯示/隱藏已完成項目 */
const hideCompleted = () => {
  isHideCompleted.value = !isHideCompleted.value;
};

/** 新增 Todo */
const handleAddTodo = (content: string) => {
  todoList.value.push({
    id: crypto.randomUUID(),
    content,
    isCompleted: false,
    isEditing: false,
    sort: todoList.value.length,
  });
};

/** 刪除 Todo */
const handleDeleteTodo = (id: string) => {
  todoList.value = todoList.value.filter((item) => item.id !== id);
};

/** 更新 Todo 內容 */
const handleUpdateTodo = (id: string, content: string) => {
  const todo = todoList.value.find((item) => item.id === id);
  if (todo) {
    todo.content = content;
  }
};

/** 切換 Todo 完成狀態 */
const handleToggleTodo = (id: string, isCompleted: boolean) => {
  const todo = todoList.value.find((item) => item.id === id);
  if (todo) {
    todo.isCompleted = isCompleted;
  }
};
</script>

<template>
  <div class="homePage">
    <div class="content-container">
      <h1 class="banner">Todo List</h1>
      <div class="todoList-wrapper">
        <FunctionalBar
          :isHideCompleted="isHideCompleted"
          @toggle-hide-completed="hideCompleted"
          @add-todo="handleAddTodo" />
        <ListPanel
          :todoList="todoList"
          :isHideCompleted="isHideCompleted"
          @delete-todo="handleDeleteTodo"
          @update-todo="handleUpdateTodo"
          @toggle-todo="handleToggleTodo" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.homePage {
  min-height: 100vh;
  width: 100%;
  background-color: #f0f2f5;
  display: flex;
  justify-content: center;
  padding-top: 50px;
}

.content-container {
  width: 100%;
  max-width: 800px;
  padding: 0 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.banner {
  text-align: center;
  margin-bottom: 30px;
  color: #2c3e50;
  font-size: 2.5rem;
  font-weight: 700;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.todoList-wrapper {
  width: 100%;
  background: white;
  border-radius: 16px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.05);
  overflow: hidden;
  display: flex;
  flex-direction: column;
  max-height: 80vh;
}
</style>
