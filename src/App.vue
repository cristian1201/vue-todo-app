<template>
  <div class="mx-auto w-full px-8 pt-6">
    <div class="mb-4">
      <div class="text-xl font-medium mb-4">Hello vue 3 - Todo Labs</div>
      <p class="text-sm text-gray-400">
        Simple todo app to explore vue 3 composition API
      </p>
    </div>
    <div class="mb-6">
      <div>
        <input
          type="text"
          v-model="newTaskInput"
          @keyup.enter="addTask"
          placeholder="Add a new task"
          class="h-10 rounded-r-none rounded shadow-lg"
        />
        <button
          class="p-2 h-10 bg-blue-500 rounded-l-none rounded-r text-white hover:bg-blue-600 shadow-lg"
          @click="addTask"
        >
          Add +
        </button>
      </div>
    </div>
    <div class="flex mb-3 text-sm text-gray-500">
      <button
        class="mr-4 pb-1"
        :class="{
          'border-b-2 border-blue-500 text-blue-500 focus:outline-none font-medium': isActiveView(
            'All'
          ),
        }"
        @click="setView('All')"
      >
        All ({{ allTasksLength }})
      </button>
      <button
        class="mr-4 pb-1"
        :class="{
          'border-b-2 border-blue-500 text-blue-500 focus:outline-none font-medium': isActiveView(
            'Ongoing'
          ),
        }"
        @click="setView('Ongoing')"
      >
        Ongoing ({{ ongoingTasksLength }})
      </button>
      <button
        class="mr-4 pb-1"
        :class="{
          'border-b-2 border-blue-500 text-blue-500 focus:outline-none font-medium': isActiveView(
            'Completed'
          ),
        }"
        @click="setView('Completed')"
      >
        Completed ({{ completedTasksLength }})
      </button>
    </div>
    <ul>
      <li v-for="taskItem in tasksInView" :key="taskItem.id">
        <input
          v-if="taskItem.edit"
          v-model="taskItem.label"
          type="text"
          @keyup.enter="confirmEdit(taskItem.id)"
        />
        <span v-else>
          {{ taskItem.label }}
        </span>
        <input
          type="checkbox"
          :checked="taskItem.complete"
          v-model="taskItem.complete"
        />
        <button @click="toggleEdit(taskItem.id)">Edit</button>
        <button @click="deleteTask(taskItem.id)">Delete</button>
      </li>
    </ul>
  </div>
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
          edit: false,
          label: 'Bread',
        },
        {
          id: uuid(),
          complete: false,
          edit: false,
          label: 'Milk',
        },
        {
          id: uuid(),
          complete: true,
          edit: false,
          label: 'Candy',
        },
      ],
    });

    // Add a new task
    const addTask = () => {
      state.taskList.push({
        id: uuid(),
        complete: false,
        edit: false,
        label: state.newTaskInput,
      });
      state.newTaskInput = '';
    };

    const findIndexTaskById = (taskId) => {
      return state.taskList.findIndex((task) => task.id === taskId);
    };

    const toggleEdit = (taskId) => {
      const taskToEdit = state.taskList[findIndexTaskById(taskId)];
      taskToEdit.edit = !taskToEdit.edit;
    };

    const confirmEdit = (taskId) => {
      state.taskList[findIndexTaskById(taskId)].edit = false;
    };

    const deleteTask = (taskId) => {
      state.taskList.splice(findIndexTaskById(taskId), 1);
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

    const isActiveView = (viewLabel) => {
      return viewLabel === state.currentView;
    };

    return {
      ...toRefs(state),
      ...toRefs(tasksViewLength),
      addTask,
      deleteTask,
      toggleEdit,
      confirmEdit,
      isActiveView,
      tasksInView,
      setView,
    };
  },
};
</script>
