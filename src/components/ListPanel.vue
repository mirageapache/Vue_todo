<script setup lang="ts">
  import { computed, ref } from 'vue';
  import type { TodoListType } from '../types/todoList';
  import swal from 'sweetalert2';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { faCheck, faPencil, faTrash, faXmark } from '@fortawesome/free-solid-svg-icons';

  const editContent = ref<string>('');
  const todoItems = ref<{ [key: string]: any }>({});

  const props = defineProps<{
    todoList: TodoListType[];
    isHideCompleted: boolean;
  }>();

  const emit = defineEmits<{
    (e: 'update:todoList', value: TodoListType[]): void;
  }>();

  /** 過濾隱藏項目 */
  const filteredTodoList = computed(() => {
    if (props.isHideCompleted) {
      return props.todoList.filter(item => !item.isCompleted);
    }
    return props.todoList;
  });

  /** 更新 todoList */
  const updateTodoList = (newList: TodoListType[]) => {
    emit('update:todoList', newList);
  };

  /** 開啟/關閉編輯狀態 */
  const handleIsEditing = (id: string, isEditing: boolean) => {
    const newList = props.todoList.map(todo => {
      if (todo.id === id) {
        todo.isEditing = isEditing;
        editContent.value = todo.content;
      }
      return todo;
    }) as TodoListType[];
    updateTodoList(newList);
    if (!isEditing) {
      editContent.value = '';
    }
  }

  /** 編輯todo */
  const editTodo = (id: string, value: string) => {
    if (value.trim() === '') {
      swal.fire({
        text: '提醒你！待辦事項不能為空白',
        icon: 'error',
      });
      return;
    }
    const newList = props.todoList.map(todo => {
      if (todo.id === id) {
        todo.content = value;
      }
      return todo;
    })
    updateTodoList(newList);
    handleIsEditing(id, false);
  }

  /** 刪除todo */
  const deleteTodo = (id: string) => {
    swal.fire({
      title: '確認刪除',
      text: '確認刪除此待辦事項嗎？',
      icon: 'warning',
      showCancelButton: true,
      confirmButtonColor: '#3085d6',
      cancelButtonColor: '#d33',
      confirmButtonText: '確認',
      cancelButtonText: '取消',
    }).then((result: any) => {
      if (result.isConfirmed) {
        const newList = props.todoList.filter(todo => todo.id !== id);
        updateTodoList(newList);
      }
    });
  }

  /** 完成todo */
  const completeTodo = (id: string, isCompleted: boolean) => {
    const todoItem = todoItems.value[id];
    if (todoItem) {
      if (isCompleted && props.isHideCompleted) todoItem.classList.add('completing'); // 完成時顯示動畫
      setTimeout(() => {
        const newList = props.todoList.map(todo => {
          if (todo.id === id) {
            todo.isCompleted = isCompleted;
          }
          return todo;
        }) as TodoListType[];
        updateTodoList(newList);
        todoItem.classList.remove('completing');
      }, 1000);
    }
  }

</script>

<template>
  <div class="todoItemList">
    <p v-if="filteredTodoList.length === 0">無任何待辦事項</p>
    <div 
      v-else 
      v-for="item in filteredTodoList" 
      :key="item.id"
      class="todoItem"
      :ref="el => { if (el) todoItems[item.id] = el }"
    >
      <div>
        <span>
          <input class="checkBox" type="checkbox" :checked="item.isCompleted" @change="completeTodo(item.id, !item.isCompleted)" />
        </span>
        <p class="todoContent" v-if="!item.isEditing">{{ item.content }}</p>
        <input class="editInput" v-else type="text" v-model="editContent" @keypress.enter="editTodo(item.id, editContent)" />
      </div>
      <div>
        <span v-if="!item.isEditing">
          <button class="btn btn-icon editButton" @click="handleIsEditing(item.id, true)">
            <FontAwesomeIcon :icon="faPencil" />
          </button>
          <button class="btn btn-icon deleteButton" @click="deleteTodo(item.id)">
            <FontAwesomeIcon :icon="faTrash" />
          </button>
        </span>
        <span v-else>
          <button class="btn btn-icon btn-danger" @click="handleIsEditing(item.id, false)">
            <FontAwesomeIcon :icon="faXmark" />
          </button>
          <button class="btn btn-icon btn-success" @click="editTodo(item.id, editContent)">
            <FontAwesomeIcon :icon="faCheck" />
          </button>
        </span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.todoItemList {
  width: 100%;
  height: 100%;
  overflow-y: auto;
}

.todoItem {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  border-radius: 5px;
  padding: 5px 10px;
  height: 50px;
  transition: all 0.3s ease;
}
.todoItem:hover {
  background-color: #f6f6f6;
  transition: background-color 0.1s ease;
}

.todoItem.completing {
  opacity: 0;
  transform: translateX(-100%);
  transition: all 1s ease;
}

.todoItem div,
.todoItem span {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.checkBox {
  display: block;
  width: 18px;
  height: 18px;
}

.todoContent {
  width: 100%;
  font-size: 16px;
  margin: 0;
  padding: 8px 10px;
}

.editInput {
  width: 100%;
  padding: 8px 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.editButton {
  color: #ff9b0f;
}
.editButton:hover {
  color: #e48704;
}

.deleteButton {
  color: #d9534f;
}
.deleteButton:hover {
  color: #d41f19;
}

</style>
