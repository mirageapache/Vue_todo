<script setup lang="ts">
  import type { TodoListType } from '../types/todoList';

  const props = defineProps<{
    todoList: TodoListType[];
  }>();

  const emit = defineEmits<{
    (e: 'update:todoList', value: TodoListType[]): void;
  }>();

  /** 更新 todoList */
  const updateTodoList = (newList: TodoListType[]) => {
    emit('update:todoList', newList);
  };

  /** 開啟/關閉編輯狀態 */
  const handleIsEditing = (id: string, isEditing: boolean) => {
    const newList = props.todoList.map(todo => {
      if (todo.id === id) {
        todo.isEditing = isEditing;
      }
      return todo;
    }) as TodoListType[];
    updateTodoList(newList);
  }

  /** 編輯todo */
  const editTodo = (id: string, value: string) => {
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
    const newList = props.todoList.filter(todo => todo.id !== id);
    updateTodoList(newList);
  }

</script>

<template>
  <div class="todoItemList">
    <p v-if="todoList.length === 0">無任何待辦事項</p>
    <div class="todoItem" v-else v-for="item in todoList" :key="item.id">
      <div>
        <input class="checkbox" type="checkbox" v-model="item.isCompleted" @change="updateTodoList([...todoList])" />
        <p class="todoContent" v-if="!item.isEditing">{{ item.content }}</p>
        <input class="editInput" v-else type="text" v-model="item.content" />
      </div>
      <div>
        <span v-if="!item.isEditing">
          <button @click="handleIsEditing(item.id, true)">編輯</button>
          <button @click="deleteTodo(item.id)">刪除</button>
        </span>
        <span v-else>
          <button @click="editTodo(item.id, item.content)">儲存</button>
          <button @click="handleIsEditing(item.id, false)">取消</button>
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
}
.todoItem:hover {
  background-color: #ccc;
  transition: background-color 0.1s ease;
}

.todoItem div,
.todoItem span {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.checkbox {
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

</style>
