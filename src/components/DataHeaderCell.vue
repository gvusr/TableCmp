<template>
  <div class="data-header-cell">
    <slot></slot>
  </div>
</template>

<script >
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'DataHeaderCell',
  props: {
    label: {
      type: String,
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
.data-header-cell {
  flex: 1;
  padding: 12px 16px;
  background-color: #f9f9f9;
  font-weight: 600;
  border-right: 1px solid #e0e0e0;
  text-align: left;
  min-height: 44px;
  display: flex;
  align-items: center;
}

.data-header-cell:last-child {
  border-right: none;
}
</style>
