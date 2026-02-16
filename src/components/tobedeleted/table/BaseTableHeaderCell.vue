<template>
  <th
    draggable="true"
    @dragstart="handleDragStart"
    @dragover="handleDragOver"
    @drop="handleDrop"
  >
    <div class="header-cell">
      <span>{{ column.label }}</span>
      <button class="remove-btn" @click.stop="$emit('remove-column', index)">✕</button>
    </div>
  </th>
</template>

<script>
export default {
  name: "BaseTableHeaderCell",
  props: {
    column: Object,
    index: Number,
    onDragStart: Function,
    onDragOver: Function,
    onDrop: Function,
  },
  methods: {
    handleDragStart(e) {
      this.onDragStart && this.onDragStart(this.index, e);
    },
    handleDragOver(e) {
      this.onDragOver && this.onDragOver(e, this.index);
    },
    handleDrop(e) {
      this.onDrop && this.onDrop(e, this.index);
    },
  },
};
</script>

<style scoped>
.header-cell {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.remove-btn {
  background: transparent;
  border: none;
  cursor: pointer;
  color: red;
  font-weight: bold;
}
</style>
