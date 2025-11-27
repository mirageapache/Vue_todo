<script setup lang="ts">
import { computed, ref } from "vue";
import type { TodoListType } from "../types/todoList";
import swal from "sweetalert2";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";
import {
  faCheck,
  faPencil,
  faTrash,
  faXmark,
  faCalendarCheck,
} from "@fortawesome/free-solid-svg-icons";

const props = defineProps<{
  todoList: TodoListType[];
  isHideCompleted: boolean;
}>();

const emit = defineEmits<{
  (e: "delete-todo", id: string): void;
  (e: "update-todo", id: string, content: string): void;
  (e: "toggle-todo", id: string, isCompleted: boolean): void;
}>();

const editContent = ref<string>("");

/** 過濾隱藏項目 */
const filteredTodoList = computed(() => {
  if (props.isHideCompleted) {
    return props.todoList.filter((item) => !item.isCompleted);
  }
  return props.todoList;
});

/** 開啟/關閉編輯狀態 */
const handleIsEditing = (id: string, isEditing: boolean) => {
  const todo = props.todoList.find((item) => item.id === id);
  if (todo) {
    todo.isEditing = isEditing;
    if (isEditing) {
      editContent.value = todo.content;
    } else {
      editContent.value = "";
    }
  }
};

/** 編輯todo */
const editTodo = (id: string, value: string) => {
  if (value.trim() === "") {
    swal.fire({
      text: "Task content cannot be empty!",
      icon: "error",
      confirmButtonColor: "#42b883",
    });
    return;
  }
  emit("update-todo", id, value);
  handleIsEditing(id, false);
};

/** 刪除todo */
const deleteTodo = (id: string) => {
  swal
    .fire({
      title: "Are you sure?",
      text: "You won't be able to revert this!",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#e53e3e",
      cancelButtonColor: "#718096",
      confirmButtonText: "Yes, delete it!",
      cancelButtonText: "Cancel",
    })
    .then((result: any) => {
      if (result.isConfirmed) {
        emit("delete-todo", id);
        swal.fire({
          title: "Deleted!",
          text: "Your file has been deleted.",
          icon: "success",
          timer: 1500,
          showConfirmButton: false,
        });
      }
    });
};

/** 完成todo */
const completeTodo = (id: string, isCompleted: boolean) => {
  emit("toggle-todo", id, isCompleted);
};
</script>

<template>
  <div class="todoItemList">
    <div v-if="filteredTodoList.length === 0" class="empty-state">
      <FontAwesomeIcon :icon="faCalendarCheck" class="empty-icon" />
      <p>No tasks found</p>
      <span class="sub-text" v-if="isHideCompleted && todoList.length > 0">
        (Check completed tasks or add a new one)
      </span>
      <span class="sub-text" v-else> Add a new task to get started! </span>
    </div>

    <transition-group name="list" tag="div" class="list-container">
      <div
        v-for="item in filteredTodoList"
        :key="item.id"
        class="todoItem"
        :class="{ completed: item.isCompleted }">
        <div class="item-content">
          <label class="checkbox-wrapper">
            <input
              type="checkbox"
              :checked="item.isCompleted"
              @change="completeTodo(item.id, !item.isCompleted)" />
            <span class="checkmark"></span>
          </label>

          <div class="text-content">
            <p
              v-if="!item.isEditing"
              class="todo-text"
              :class="{ 'text-crossed': item.isCompleted }">
              {{ item.content }}
            </p>
            <input
              v-else
              class="editInput"
              type="text"
              v-model="editContent"
              @keypress.enter="editTodo(item.id, editContent)"
              ref="editInputRef"
              v-focus />
          </div>
        </div>

        <div class="actions">
          <template v-if="!item.isEditing">
            <button
              class="btn-icon edit"
              @click="handleIsEditing(item.id, true)"
              title="Edit">
              <FontAwesomeIcon :icon="faPencil" />
            </button>
            <button
              class="btn-icon delete"
              @click="deleteTodo(item.id)"
              title="Delete">
              <FontAwesomeIcon :icon="faTrash" />
            </button>
          </template>
          <template v-else>
            <button
              class="btn-icon save"
              @click="editTodo(item.id, editContent)"
              title="Save">
              <FontAwesomeIcon :icon="faCheck" />
            </button>
            <button
              class="btn-icon cancel"
              @click="handleIsEditing(item.id, false)"
              title="Cancel">
              <FontAwesomeIcon :icon="faXmark" />
            </button>
          </template>
        </div>
      </div>
    </transition-group>
  </div>
</template>

<style scoped>
.todoItemList {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  padding: 0;
}

/* Scrollbar styling */
.todoItemList::-webkit-scrollbar {
  width: 8px;
}
.todoItemList::-webkit-scrollbar-track {
  background: #f1f1f1;
}
.todoItemList::-webkit-scrollbar-thumb {
  background: #cbd5e0;
  border-radius: 4px;
}
.todoItemList::-webkit-scrollbar-thumb:hover {
  background: #a0aec0;
}

.list-container {
  display: flex;
  flex-direction: column;
}

.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 60px 20px;
  color: #a0aec0;
  text-align: center;
}

.empty-icon {
  font-size: 48px;
  margin-bottom: 16px;
  opacity: 0.5;
}

.empty-state p {
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 8px 0;
}

.sub-text {
  font-size: 14px;
}

.todoItem {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px 24px;
  border-bottom: 1px solid #f0f0f0;
  background-color: white;
  transition: all 0.2s ease;
}

.todoItem:hover {
  background-color: #f8f9fa;
}

.todoItem:last-child {
  border-bottom: none;
}

.item-content {
  display: flex;
  align-items: center;
  gap: 16px;
  flex: 1;
  min-width: 0; /* Allow text truncation */
}

/* Custom Checkbox */
.checkbox-wrapper {
  display: block;
  position: relative;
  padding-left: 24px;
  cursor: pointer;
  user-select: none;
}

.checkbox-wrapper input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

.checkmark {
  position: absolute;
  top: -10px;
  left: 0;
  height: 20px;
  width: 20px;
  background-color: #fff;
  border: 2px solid #cbd5e0;
  border-radius: 6px;
  transition: all 0.2s ease;
}

.checkbox-wrapper:hover input ~ .checkmark {
  border-color: #42b883;
}

.checkbox-wrapper input:checked ~ .checkmark {
  background-color: #42b883;
  border-color: #42b883;
}

.checkmark:after {
  content: "";
  position: absolute;
  display: none;
  left: 6px;
  top: 2px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.checkbox-wrapper input:checked ~ .checkmark:after {
  display: block;
}

.text-content {
  flex: 1;
  min-width: 0;
}

.todo-text {
  margin: 0;
  font-size: 16px;
  color: #2c3e50;
  word-break: break-word;
  transition: color 0.2s ease;
}

.text-crossed {
  text-decoration: line-through;
  color: #a0aec0;
}

.editInput {
  width: 100%;
  padding: 8px 12px;
  border: 2px solid #42b883;
  border-radius: 6px;
  font-size: 16px;
  outline: none;
}

.actions {
  display: flex;
  gap: 8px;
  margin-left: 16px;
  opacity: 0;
  transform: translateX(10px);
  transition: all 0.2s ease;
}

.todoItem:hover .actions,
.todoItem:focus-within .actions {
  opacity: 1;
  transform: translateX(0);
}

.btn-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.2s ease;
  background-color: transparent;
}

.btn-icon.edit {
  color: #718096;
}
.btn-icon.edit:hover {
  background-color: #edf2f7;
  color: #4a5568;
}

.btn-icon.delete {
  color: #cbd5e0;
}
.btn-icon.delete:hover {
  background-color: #fff5f5;
  color: #e53e3e;
}

.btn-icon.save {
  color: #42b883;
  background-color: #f0fff4;
}
.btn-icon.save:hover {
  background-color: #c6f6d5;
}

.btn-icon.cancel {
  color: #e53e3e;
  background-color: #fff5f5;
}
.btn-icon.cancel:hover {
  background-color: #fed7d7;
}

/* List Transitions */
.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
</style>
