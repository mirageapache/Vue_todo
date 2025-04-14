<script setup lang="ts">
  import { ref } from 'vue';
  import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
  import { faSearch } from '@fortawesome/free-solid-svg-icons';

  const props = defineProps<{
    todoList: { name: string, isCompleted: boolean }[];
    isHideCompleted: boolean;
    hideCompleted: () => void;
  }>();
  const todo = ref<string>("");

  /** 新增項目 */
  const addTodo = () => {
    if (todo.value.trim() === '' || todo.value.trim().length === 0) return;
    props.todoList.push({ name: todo.value, isCompleted: false });
    todo.value = '';
    localStorage.setItem('todoList', JSON.stringify(props.todoList));
  };

</script>

<template>
  <div class="todoItem">
    <span class="input-wrapper">
      <input class="todoInput" type="text" v-model="todo" placeholder="add a Todo" @keypress.enter="addTodo"/>
      <FontAwesomeIcon class="search-icon"  :icon="faSearch" @click="addTodo" />
    </span>
    <span class="functionalButton">
      <button class="hideButton" type="button" @click="hideCompleted">
        {{ isHideCompleted ? '顯示已完成項目' : '隱藏已完成項目' }}
      </button>
    </span>
  </div>
</template>

<style scoped>
.todoItem {
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

.search-icon {
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

.hideButton {
  border: 1px solid #ccc;
  background-color: #fff;
  font-size: 16px;
  padding: 8px 10px;
  border-radius: 5px;
}
.hideButton:hover {
  background-color: #eee;
  transition: background-color 0.3s ease;
}

</style>