<template>
  <div :key="task.id" v-for="task in taskList">
    <TaskItem
      :task="task"
      @task-completed="(...args) => $emit('taskCompleted', ...args)"
      @task-edited="(...args) => $emit('taskEdited', ...args)"
      @task-deleted="(...args) => $emit('taskDeleted', ...args)"
    />
  </div>
</template>

<script setup>
import TaskItem from "./TaskItem.vue";

defineProps({
  taskList: {
    type: Array,
    required: true,
  },
});

defineEmits(["taskCompleted", "taskEdited", "taskDeleted"]);
</script>
