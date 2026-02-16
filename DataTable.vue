<!-- DataTable.vue -->
<template>
  <div :class="['data-table', view]" ref="tableRef">
    <slot></slot>
    <div
      v-if="dragging && dragIndicatorStyle"
      class="drag-indicator"
      :style="dragIndicatorStyle"
    ></div>
  </div>
</template>

<script>
export default {
  name: "DataTable",
  props: {
    view: {
      type: String,
      default: "horizontal",
      validator: (v) => ["horizontal", "vertical"].includes(v),
    },
    dragging: Boolean,
    dragIndicatorStyle: Object,
  },
};
</script>

<style scoped>
.data-table {
  border-collapse: collapse;
  width: 100%;
  position: relative;
  user-select: none;
  font-family: Arial, sans-serif;
  font-size: 14px;
  display: table;
  table-layout: fixed; /* Fix column widths to improve pixel alignment */
}

.data-row {
  display: table-row;
  cursor: default;
}

.data-header-cell,
.data-cell {
  display: table-cell;
  border: 1px solid #ccc;
  padding: 8px 12px;
  white-space: nowrap;
  vertical-align: middle;
  overflow: hidden;
  text-overflow: ellipsis;
}

.data-header-cell {
  font-weight: bold;
  background-color: #f5f5f5;
  position: relative;
  user-select: none;
}

/* Drag indicator line */
.drag-indicator {
  position: absolute;
  top: 0;
  width: 3px;
  background: linear-gradient(180deg, rgba(0, 123, 255, 0.7) 0%, rgba(0, 123, 255, 0.4) 100%);
  box-shadow: 0 0 8px rgba(0, 123, 255, 0.8);
  border-radius: 2px;
  transition: top 0.2s ease, left 0.2s ease, height 0.2s ease;
  pointer-events: none;
  z-index: 9999;
}
</style>