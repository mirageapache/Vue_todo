<script setup lang="ts">
  import { ref } from 'vue';
  import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
  import { faEye, faEyeSlash, faXmark } from '@fortawesome/free-solid-svg-icons';
  import type { TodoListType } from '../types/todoList';

  const props = defineProps<{
    todoList: TodoListType[];
    isHideCompleted: boolean;
    hideCompleted: () => void;
  }>();
  const todo = ref<string>("");

  /** 新增項目 */
  const addTodo = () => {
    if (todo.value.trim() === '' || todo.value.trim().length === 0) return;
    props.todoList.push({
      id: crypto.randomUUID(),
      content: todo.value,
      isCompleted: false,
      isEditing: false,
      sort: props.todoList.length,
    });
    todo.value = '';
    localStorage.setItem('todoList', JSON.stringify(props.todoList));
  };

</script>

<template>
  <div class="toolBar">
    <span class="input-wrapper">
      <input class="todoInput" type="text" v-model="todo" placeholder="add a Todo" @keypress.enter="addTodo"/>
      <FontAwesomeIcon class="clear-icon"  :icon="faXmark" @click="() => { todo = '' }" />
    </span>
    <button class="btn btn-primary" @click="addTodo">新增</button>
    <span class="functionalButton">
      <button class="btn btn-light" type="button" @click="hideCompleted">
        <FontAwesomeIcon :icon="isHideCompleted ? faEyeSlash : faEye" style="color: #666; width: 20px;" />
      </button>
    </span>
  </div>
</template>

<style scoped>
.toolBar {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 10px;
  width: 100%;
  margin-bottom: 10px;
  border-bottom: 1px solid #ccc;
  padding-bottom: 10px;
}

.input-wrapper {
  position: relative;
  display: inline-block;
}

.clear-icon {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: #666;
  cursor: pointer;
  font-size: 16px;
}

.todoInput {
  width: 300px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 8px;
  font-size: 16px;
  padding-right: 30px;
}
.todoInput:focus {
  outline: none;
}

.functionalButton {
  position: absolute;
  top:0;
  right: 10px;
}
</style>