<template>
  <td class="data-cell" :class="{ 'data-cell--emphasized': emphasized }">
    <slot></slot>
  </td>
</template>

<script>
export default {
  name: 'DataCell',
  props: {
    emphasized: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {}
  }
}
</script>

<style scoped>
.data-cell {
  padding: 0.75rem 1rem;
  color: #374151;
  font-size: 0.875rem;
}

.data-cell--emphasized {
  font-weight: 600;
  color: #1f2937;
}
</style>
