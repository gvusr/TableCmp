<template>
  <div class="data-row">
    <slot></slot>
  </div>
</template>

<script >
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'DataRow',
  props: {
    rowData: {
      type: Object,
      default: () => ({})
    }
  }
});
</script>

<style scoped>
.data-row {
  display: flex;
  width: 100%;
  border-bottom: 1px solid #e0e0e0;
}

.data-row:hover {
  background-color: #f5f5f5;
}
</style>
