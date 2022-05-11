<template>
  <div>
    <div :class="$style.form">
      <AddForm @task-added="addTask" />
    </div>
    <div :class="$style.tasklist">
      <TaskList
        :taskList="taskList"
        @task-completed="completeTask"
        @task-edited="updateTask"
        @task-deleted="deleteTask"
      />
    </div>
  </div>
</template>

<style module>
.form {
  margin-block-start: 40px;
  display: flex;
  justify-content: center;
}

.tasklist {
  margin-block: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  row-gap: 10px;
}
</style>

<script setup>
import { onBeforeMount, ref } from "vue";
import AddForm from "../forms/AddForm.vue";
import TaskList from "../tasklist/TaskList.vue";
import buildRequest from "../../../utils/buildRequest.js";

const taskList = ref([]);

onBeforeMount(async () => {
  const data = await buildRequest("get", "http://localhost:8000/api/tasks");
  taskList.value = data;
});

async function addTask(userId, description) {
  const data = await buildRequest("post", "http://localhost:8000/api/tasks", {
    description,
    user_id: userId,
  });

  taskList.value.push(data);
}

async function completeTask(id, completed) {
  const data = await buildRequest(
    "patch",
    `http://localhost:8000/api/tasks/${id}`,
    { completed }
  );

  const index = taskList.value.findIndex((task) => task.id === id);
  taskList.value.splice(index, 1, data);
}

async function updateTask(id, description, userId) {
  const data = await buildRequest(
    "patch",
    `http://localhost:8000/api/tasks/${id}`,
    { user_id: userId, description }
  );

  const index = taskList.value.findIndex((task) => task.id === id);
  taskList.value.splice(index, 1, data);
}

async function deleteTask(id) {
  await buildRequest("delete", `http://127.0.0.1:8000/api/tasks/${id}`);
  taskList.value = taskList.value.filter((task) => task.id !== id);
}
</script>
