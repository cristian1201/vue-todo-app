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
    </li>
  </ul>
</template>

<script>
import { reactive, toRefs, computed } from 'vue';
export default {
  name: 'App',
  setup() {
    // Basic state
    const state = reactive({
      currentView: 'All',
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

    // Set the current view
    const setView = (viewLabel) => {
      state.currentView = viewLabel;
    };

    const tasksListByStatus = reactive({
      // All tasks
      allTasks: computed(() => {
        return state.taskList;
      }),

      // Ongoing tasks
      ongoingTasks: computed(() => {
        return state.taskList.filter((task) => task.complete === false);
      }),

      // Completed tasks
      completedTasks: computed(() => {
        return state.taskList.filter((task) => task.complete === true);
      }),
    });

    const tasksInView = computed(() => {
      if (state.currentView === 'Ongoing') {
        return tasksListByStatus.ongoingTasks;
      } else if (state.currentView === 'Completed') {
        return tasksListByStatus.completedTasks;
      } else {
        return tasksListByStatus.allTasks;
      }
    });

    const tasksViewLength = reactive({
      allTasksLength: computed(() => {
        return tasksListByStatus.allTasks.length;
      }),
      ongoingTasksLength: computed(() => {
        return tasksListByStatus.ongoingTasks.length;
      }),
      completedTasksLength: computed(() => {
        return tasksListByStatus.completedTasks.length;
      }),
    });

    return {
      ...toRefs(state),
      ...toRefs(tasksViewLength),
      addTask,
      tasksInView,
      setView,
    };
  },
};
</script>