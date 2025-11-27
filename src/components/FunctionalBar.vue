<script setup lang="ts">
import { ref } from "vue";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import {
  faEye,
  faEyeSlash,
  faPlus,
  faXmark,
} from "@fortawesome/free-solid-svg-icons";

defineProps<{
  isHideCompleted: boolean;
}>();

const emit = defineEmits<{
  (e: "add-todo", content: string): void;
  (e: "toggle-hide-completed"): void;
}>();

const todo = ref<string>("");

/** 新增項目 */
const addTodo = () => {
  if (todo.value.trim() === "") return;
  emit("add-todo", todo.value);
  todo.value = "";
};
</script>

<template>
  <div class="toolBar">
    <div class="input-group">
      <div class="input-wrapper">
        <input
          class="todoInput"
          type="text"
          v-model="todo"
          placeholder="Add a new task..."
          @keypress.enter="addTodo" />
        <transition name="fade">
          <button v-if="todo" class="clear-btn" @click="todo = ''">
            <FontAwesomeIcon :icon="faXmark" />
          </button>
        </transition>
      </div>
      <button class="btn-add" @click="addTodo" :disabled="!todo.trim()">
        <FontAwesomeIcon :icon="faPlus" />
        <span>Add</span>
      </button>
    </div>

    <button
      class="btn-toggle"
      :class="{ active: isHideCompleted }"
      @click="emit('toggle-hide-completed')"
      :title="
        isHideCompleted ? 'Show completed tasks' : 'Hide completed tasks'
      ">
      <FontAwesomeIcon :icon="isHideCompleted ? faEyeSlash : faEye" />
    </button>
  </div>
</template>

<style scoped>
.toolBar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  background-color: #fff;
  border-bottom: 1px solid #f0f0f0;
  gap: 16px;
}

.input-group {
  display: flex;
  flex: 1;
  gap: 12px;
}

.input-wrapper {
  position: relative;
  flex: 1;
  display: flex;
  align-items: center;
}

.todoInput {
  width: 100%;
  padding: 12px 16px;
  padding-right: 40px;
  border: 2px solid #eef0f5;
  border-radius: 12px;
  font-size: 16px;
  transition: all 0.3s ease;
  background-color: #f8f9fa;
  color: #2c3e50;
}

.todoInput:focus {
  outline: none;
  border-color: #42b883;
  background-color: #fff;
  box-shadow: 0 0 0 4px rgba(66, 184, 131, 0.1);
}

.todoInput::placeholder {
  color: #a0aec0;
}

.clear-btn {
  position: absolute;
  right: 12px;
  background: none;
  border: none;
  color: #a0aec0;
  cursor: pointer;
  padding: 4px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.clear-btn:hover {
  color: #e53e3e;
  background-color: #fff5f5;
}

.btn-add {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0 24px;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-add:hover:not(:disabled) {
  background-color: #3aa876;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(66, 184, 131, 0.3);
}

.btn-add:active:not(:disabled) {
  transform: translateY(0);
}

.btn-add:disabled {
  background-color: #cbd5e0;
  cursor: not-allowed;
  opacity: 0.7;
}

.btn-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  border: 2px solid #eef0f5;
  background-color: white;
  border-radius: 12px;
  color: #718096;
  cursor: pointer;
  transition: all 0.3s ease;
}

.btn-toggle:hover {
  border-color: #cbd5e0;
  color: #4a5568;
  background-color: #f7fafc;
}

.btn-toggle.active {
  background-color: #ebf8ff;
  border-color: #bee3f8;
  color: #3182ce;
}

/* Transitions */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
