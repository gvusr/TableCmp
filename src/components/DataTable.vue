<template>
  <div class="data-table" :class="[viewMode]">
    <slot></slot>
  </div>
</template>

<script >
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'DataTable',
  props: {
    isHorizontal: {
      type: Boolean,
      default: true
    }
  },
  computed: {
    viewMode() {
      return this.isHorizontal ? 'horizontal' : 'vertical';
    }
  }
});
</script>

<style scoped>
.data-table {
  width: 100%;
  border-collapse: collapse;
}

.data-table.horizontal {
  display: flex;
  flex-direction: column;
}

.data-table.vertical {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
</style>
