<template>
  <div class="data-cell">
    <slot></slot>
  </div>
</template>

<script >
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'DataCell',
  props: {
    value: {
      type: [String, Number, Boolean],
      default: ''
    },
    width: {
      type: String,
      default: 'auto'
    }
  }
});
</script>

<style scoped>
.data-cell {
  flex: 1;
  padding: 12px 16px;
  border-right: 1px solid #e0e0e0;
  display: flex;
  align-items: center;
  min-height: 44px;
}

.data-cell:last-child {
  border-right: none;
}
</style>
