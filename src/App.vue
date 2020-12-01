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
    <button style="margin-right: 20px" @click="setView('All')">
      All ({{ allTasksLength }})
    </button>
    <button style="margin-right: 20px" @click="setView('Ongoing')">
      Ongoing ({{ ongoingTasksLength }})
    </button>
    <button style="margin-right: 20px" @click="setView('Completed')">
      Completed ({{ completedTasksLength }})
    </button>
  </div>
  <ul>
    <li v-for="taskItem in tasksInView" :key="taskItem.label">
      {{ taskItem.label }}
      <input
        type="checkbox"
        :checked="taskItem.complete"
        v-model="taskItem.complete"
      />
      <button>Edit</button>
      <button @click="deleteTask(taskItem.id)">Delete</button>
    </li>
  </ul>
</template>

<script>
import { reactive, toRefs, computed } from 'vue';
import { v4 as uuid } from 'uuid';
export default {
  name: 'App',
  setup() {
    // Basic state
    const state = reactive({
      currentView: 'All',
      newTaskInput: '',
      taskList: [
        {
          id: uuid(),
          complete: false,
          label: 'Bread',
        },
        {
          id: uuid(),
          complete: false,
          label: 'Milk',
        },
        {
          id: uuid(),
          complete: true,
          label: 'Candy',
        },
      ],
    });

    // Add a new task
    const addTask = () => {
      state.taskList.push({
        id: uuid(),
        complete: false,
        label: state.newTaskInput,
      });
      state.newTaskInput = '';
    };

    const deleteTask = (taskId) => {
      const taskIndex = state.taskList.findIndex((task) => task.id === taskId);
      state.taskList.splice(taskIndex, 1);
    };

    // Set the current view
    const setView = (viewLabel) => {
      state.currentView = viewLabel;
    };

    const taskLists = reactive({
      // All tasks
      all: computed(() => state.taskList),

      // Ongoing tasks
      ongoing: computed(() =>
        state.taskList.filter((task) => task.complete === false)
      ),

      // Completed tasks
      completed: computed(() =>
        state.taskList.filter((task) => task.complete === true)
      ),
    });

    const tasksInView = computed(() => {
      if (state.currentView === 'Ongoing') {
        return taskLists.ongoing;
      } else if (state.currentView === 'Completed') {
        return taskLists.completed;
      } else {
        return taskLists.all;
      }
    });

    const tasksViewLength = reactive({
      allTasksLength: computed(() => taskLists.all.length),
      ongoingTasksLength: computed(() => taskLists.ongoing.length),
      completedTasksLength: computed(() => taskLists.completed.length),
    });

    return {
      ...toRefs(state),
      ...toRefs(tasksViewLength),
      addTask,
      deleteTask,
      tasksInView,
      setView,
    };
  },
};
</script>
