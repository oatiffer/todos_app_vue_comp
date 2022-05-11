<template>
  <div
    :class="checked ? $style.checked : $style.checkbox"
    @click="completeTask"
  >
    <i class="fas fa-check"></i>
  </div>
</template>

<style module>
.checkbox,
.checked {
  height: 20px;
  width: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: whitesmoke;
  border-radius: 50%;
  cursor: pointer;
  transition: ease 0.2s;
  margin-inline: 20px;
}

.checked {
  background-color: lightblue;
}
</style>

<script setup>
import { ref, defineEmits } from "vue";

const checked = ref(props.task.completed);
const emit = defineEmits(["taskCompleted"]);

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
});

function completeTask() {
  checked.value = !checked.value;
  emit("taskCompleted", props.task.id, checked.value);
}
</script>
