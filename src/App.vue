<template>
  <h2>Hello vue 3 - Todo Labs</h2>
  <div style="margin-bottom: 20px">
    <div>
      <input
        type="text"
        v-model="newTaskInput"
        @keyup.enter="addTask"
        placeholder="Add a new task"
      />
      <button @click="addTask">Add +</button>
    </div>
  </div>
  <div style="display: flex">
    <div style="margin-right: 20px">All</div>
    <div style="margin-right: 20px">Ongoing</div>
    <div style="margin-right: 20px">Completed</div>
  </div>
  <ul>
    <li v-for="taskItem in taskList" :key="taskItem.label">
      {{ taskItem.label }}
      <input
        type="checkbox"
        :checked="taskItem.complete"
        v-model="taskItem.complete"
      />
    </li>
  </ul>
</template>

<script>
import { reactive, toRefs } from 'vue';
export default {
  name: 'App',
  setup() {
    const state = reactive({
      newTaskInput: '',
      taskList: [
        {
          complete: false,
          label: 'Bread',
        },
        {
          complete: false,
          label: 'Milk',
        },
        {
          complete: true,
          label: 'Candy',
        },
      ],
    });

    // Add a new task
    const addTask = () => {
      state.taskList.push({
        label: state.newTaskInput,
        complete: false,
      });
      state.newTaskInput = '';
    };

    return {
      ...toRefs(state),
      addTask,
    };
  },
};
</script>
