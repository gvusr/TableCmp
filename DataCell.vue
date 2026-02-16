<!-- DataCell.vue -->
<template>
  <div class="data-cell">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "DataCell",
};
</script>

<style scoped>
.data-cell {
  display: table-cell;
  border: 1px solid #ccc;
  padding: 8px 12px;
  white-space: nowrap;
  vertical-align: middle;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>