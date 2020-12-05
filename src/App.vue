<template>
  <div class="mx-auto w-full px-8 pt-6">
    <div class="mb-4">
      <div class="text-xl font-medium mb-4">Hello vue 3 - Todo Labs</div>
      <p class="text-sm text-gray-400">
        Simple todo app to explore vue 3 composition API
      </p>
    </div>
    <div class="mb-6">
      <div class="w-full flex">
        <div class="flex-grow">
          <input
            type="text"
            v-model="newTaskInput"
            @keyup.enter="addTask"
            placeholder="Add a new task"
            class="w-full h-10 mb-2 rounded-r-none rounded shadow-lg"
          />
          <p v-if="newTaskInputError" class="text-sm text-red-600">
            {{ newTaskInputError }}
          </p>
        </div>
        <button
          class="p-2 h-10 bg-blue-500 rounded-l-none rounded-r text-white hover:bg-blue-600 shadow-lg"
          @click="addTask"
        >
          Add +
        </button>
      </div>
    </div>
    <TabNav
      :current-view="currentView"
      :items="taskListOverview"
      @update-current-view="setView"
    />
    <ul class="relative">
      <transition name="mode-fade" mode="out-in">
      <div v-if="tasksInView.length > 0" key="tasks">
        <transition-group name="list-complete" tag="li">
        <li
          v-for="(taskItem, i) in tasksInView"
          :key="taskItem.id"
          class="px-3 py-2 w-full rounded-md mb-3 border flex justify-between items-center hover:border-gray-400 transition-all duration-200 ease-in"
        >
          <div class="flex items-center w-full justify-between">
            <div class="flex flex-grow items-center">
              <input
                type="checkbox"
                :checked="taskItem.complete"
                v-model="taskItem.complete"
                class="mr-3"
              />
                <input
                  :ref="
                    (el) => {
                      if (el) editTasks[i] = el;
                    }
                  "
                  v-show="taskItem.edit"
                  v-model="taskItem.label"
                  type="text"
                  class="h-10 w-full mr-2 rounded border border-gray-300"
                  @keyup.enter="confirmEdit(taskItem.id)"
                />
              <transition name="mode-fade">
                <span
                  v-show="!taskItem.edit"
                  class="flex text-gray-700 items-center h-10"
                  @dblclick="toggleEdit(taskItem.id)"
                >
                  {{ taskItem.label }}
                </span>
              </transition>
            </div>
            <div class="flex">
              <button
                v-if="!taskItem.edit"
                class="text-gray-500 mr-1"
                @click="toggleEdit(taskItem.id)"
              >
                <IconPencil></IconPencil>
              </button>
              <button
                v-else
                class="text-white bg-green-500 rounded-full mr-1"
                @click="confirmEdit(taskItem.id)"
              >
                <IconCheck></IconCheck>
              </button>
              <button
                class="text-gray-500 mr-1"
                @click="deleteTask(taskItem.id)"
              >
                <IconTrash />
              </button>
            </div>
          </div>
        </li>
        </transition-group>
      </div>
      <p v-else key="notasks" class="absolute text-sm text-gray-600">No tasks here... 😢</p>
      </transition>
    </ul>
  </div>
</template>

<script>
import { ref, onBeforeUpdate, reactive, toRefs, computed, nextTick } from 'vue';
import { v4 as uuid } from 'uuid';

// Icon
import IconPencil from './components/IconPencil.vue';
import IconTrash from './components/IconTrash.vue';
import IconCheck from './components/IconCheck.vue';

import TabNav from './components/TabNav.vue';

export default {
  name: 'App',
  components: {
    IconPencil,
    IconTrash,
    IconCheck,
    TabNav,
  },
  setup() {
    // Basic state
    const state = reactive({
      currentView: 'All',
      newTaskInput: '',
      newTaskInputError: '',
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

    const editTasks = ref([HTMLElement]);

    // Add a new task
    const addTask = () => {
      if (state.newTaskInput !== '') {
        state.taskList.push({
          id: uuid(),
          complete: false,
          edit: false,
          label: state.newTaskInput,
        });
        state.newTaskInput = '';
        if (state.newTaskInputError) {
          state.newTaskInputError = '';
        }
      } else {
        state.newTaskInputError = 'This field cannot be empty';
      }
    };

    const findIndexTaskById = (taskId) => {
      return state.taskList.findIndex((task) => task.id === taskId);
    };

    const taskListOverview = reactive([
      {
        value: 'All',
        label: computed(() => 'All ' + '(' + taskLists.all.length + ')'),
      },
      {
        value: 'Ongoing',
        label: computed(
          () => 'Ongoing ' + '(' + taskLists.ongoing.length + ')'
        ),
      },
      {
        value: 'Completed',
        label: computed(
          () => 'Completed ' + '(' + taskLists.completed.length + ')'
        ),
      },
    ]);

    const toggleEdit = async (taskId) => {
      const taskToEdit = state.taskList[findIndexTaskById(taskId)];
      taskToEdit.edit = true;
      await nextTick();
      editTasks.value[findIndexTaskById(taskId)].focus();
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

    return {
      ...toRefs(state),
      ...toRefs(tasksViewLength),
      addTask,
      deleteTask,
      toggleEdit,
      confirmEdit,
      tasksInView,
      taskListOverview,
      setView,
      editTasks,
    };
  },
};
</script>

<style>
.list-complete-enter-from {
  opacity: 0;
  transform: translateX(20px);
}
.list-complete-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.list-complete-leave-active {
  position: absolute;
}

.mode-fade-enter-active, .mode-fade-leave-active {
  transition: opacity .2s
}

.mode-fade-enter-from, .mode-fade-leave-to {
  opacity: 0
}

</style>
