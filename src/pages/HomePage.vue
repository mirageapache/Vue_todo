<script setup lang="ts">
  import { onMounted, reactive, ref } from 'vue';
  import FunctionalBar from '../components/FunctionalBar.vue';
  const todoList = reactive<{ name: string, isCompleted: boolean }[]>([]);
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

</script>

<template>
  <div class="homePage">
    <h1 class="banner">Todo List</h1>
    <div class="todoList">
      <FunctionalBar :todoList="todoList" :isHideCompleted="isHideCompleted" :hideCompleted="hideCompleted" />
      <div class="todoItemList">
        <p v-if="todoList.length === 0">No Todo items</p>
        <div class="todoItem" v-else v-for="item in todoList">
          <input type="checkbox" v-model="item.isCompleted" />
          <p>{{ item.name }}</p>
        </div>
      </div>
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
  overflow-y: auto;
}

</style>

