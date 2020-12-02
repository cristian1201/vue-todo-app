<template>
  <nav>
    <ul class="flex mb-3 text-sm text-gray-500">
      <TabNavItem
        v-for="item in items"
        :item="item"
        :isActive="currentView === item.value"
        class="mr-3"
        @emit-tab-value="updateCurrentView"
      />
    </ul>
  </nav>
</template>

<script>
import TabNavItem from './TabNavItem.vue';
export default {
  name: 'TabNav',
  components: {
    TabNavItem,
  },
  props: {
    currentView: {
      type: String,
      default: 'All',
      validator: (value) => {
        return ['All', 'Ongoing', 'Completed'].indexOf(value) !== -1;
      },
    },
    items: {
      type: Array,
      required: true,
    },
  },
  setup(_, ctx) {
    const updateCurrentView = (payload) => {
      ctx.emit('update-current-view', payload.value);
    };

    return {
      updateCurrentView,
    };
  },
};
</script>
