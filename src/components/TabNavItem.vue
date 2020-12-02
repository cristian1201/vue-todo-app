<template>
  <li>
    <button
      class="pb-1"
      :class="{
        'border-b-2 border-blue-500 text-blue-500 focus:outline-none font-medium': isActive,
      }"
      @click="emitTabValue"
    >
      {{ item.label }}
    </button>
  </li>
</template>

<script>
export default {
  name: 'TabNavItem',
  props: {
    item: {
      type: Object,
      required: true,
    },
    isActive: {
      type: Boolean,
      default: false,
    },
  },
  setup(props, ctx) {
    const emitTabValue = () => {
      ctx.emit('emit-tab-value', {
        value: props.item.value,
      });
    };
    return {
      emitTabValue,
    };
  },
};
</script>

<style lang="scss" scoped></style>
