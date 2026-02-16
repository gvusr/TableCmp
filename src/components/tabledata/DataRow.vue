<template>
  <tr class="data-row" :class="{ 'data-row--hoverable': hoverable }">
    <slot></slot>
  </tr>
</template>

<script>
export default {
  name: 'DataRow',
  props: {
    hoverable: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {}
  }
}
</script>

<style scoped>
.data-row {
  border-bottom: 1px solid #e5e7eb;
  transition: background-color 0.2s ease;
}

.data-row--hoverable:hover {
  background-color: #f9fafb;
}
</style>
