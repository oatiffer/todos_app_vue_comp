<template>
  <form :class="$style.form" @submit.prevent="handleSubmit">
    <div :class="$style.inputContainer">
      <div :class="$style.textContainer">
        <textarea
          :class="$style.text"
          id="description"
          name="description"
          v-model="description"
          @blur="v$.description.$touch()"
        ></textarea>
        <div v-if="v$.description.$error">
          <span
            :key="error.$uid"
            v-for="error in v$.description.$errors"
            :class="$style.error"
          >
            {{ error.$message }}
          </span>
        </div>
      </div>
      <div :class="$style.selectContainer">
        <label :class="$style.label" for="user"> Assign to: </label>
        <select
          :class="$style.select"
          id="user"
          name="user_id"
          v-model="userId"
          @blur="v$.userId.$touch()"
        >
          <option :key="user.id" v-for="user in userList" :value="user.id">
            {{ user.name }}
          </option>
        </select>
        <div v-if="v$.userId.$error">
          <span
            :key="error.$uid"
            v-for="error in v$.userId.$errors"
            :class="$style.error"
          >
            {{ error.$message }}
          </span>
        </div>
      </div>
    </div>
    <div :class="$style.btnContainer">
      <button :class="$style.submit" type="submit">
        <i class="fas fa-save fa-lg"></i>
      </button>
    </div>
  </form>
</template>

<style module>
.form {
  padding-block: 10px;
  display: flex;
}

.inputContainer {
  display: flex;
  flex-direction: column;
  row-gap: 10px;
}

.btnContainer {
  margin-inline-start: 10px;
  display: flex;
  align-items: center;
}

.textContainer {
  display: flex;
  flex-direction: column;
}
.selectContainer {
  display: flex;
  align-items: center;
  column-gap: 4px;
}

.text {
  width: 456px;
  height: 40px;
  outline: none;
  appearance: none;
  color: gray;
  font-size: 0.8rem;
  font-family: "Manrope", sans-serif;
  font-size: 0.8rem;
  border: none;
  border-radius: 4px;
  padding-inline: 4px;
  background-color: whitesmoke;
}

.select {
  width: 412px;
  outline: none;
  color: gray;
  font-size: 0.8rem;
  font-family: "Manrope", sans-serif;
  border-radius: 4px;
  border-color: lightgray;
  background-color: whitesmoke;
  padding-inline: 4px;
  margin-inline-start: 4px;
}

.error {
  color: crimson;
  font-weight: 700;
  font-size: 0.7rem;
}

.submit {
  height: 30px;
  width: 30px;
  border: none;
  border-radius: 4px;
  appearance: none;
  background-color: transparent;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: ease 0.2s;
  position: absolute;
  inset-inline-end: 26px;
}

.submit:hover {
  background-color: lightblue;
}

i {
  transition: ease 0.2s;
}

.submit:hover > i {
  color: rgb(70, 72, 75);
}
</style>

<script setup>
import buildRequest from "../../../utils/buildRequest.js";
import useVuelidate from "@vuelidate/core";
import { required, maxLength } from "@vuelidate/validators";
import { onBeforeMount, ref } from "vue";

const userId = ref(props.task.user.id);
const description = ref(props.task.description);
const userList = ref([]);

const props = defineProps({
  task: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(["setEditingFalse", "taskEdited"]);

const rules = {
  userId: { required, numeric: true },
  description: { required, maxLength: maxLength(200) },
};

const v$ = useVuelidate(rules, { userId, description });

onBeforeMount(async () => {
  const data = await buildRequest("get", "http://127.0.0.1:8000/api/users");
  userList.value = data;
});

async function handleSubmit() {
  const formValid = await v$.value.$validate();
  if (!formValid) {
    return;
  }

  emit("taskEdited", props.task.id, description.value, userId.value);
  emit("setEditingFalse");
}
</script>
