<template>
  <th
    draggable="true"
    :class="{
      sorted: sorted,
      'sort-asc': sorted && sortDir === 1,
      'sort-desc': sorted && sortDir === -1,
    }"
    @click="onSort"
    @dragstart="onDragStart"
    @dragover="onDragOver"
    @dragend="onDragEnd"
  >
    <slot />
    <button
      type="button"
      class="close-btn"
      @click.stop="onRemove"
      aria-label="Remove Column"
      title="Remove Column"
    >
      ×
    </button>
    <div v-if="dragLineVisible" class="drag-indicator" :style="{ left: dragLineLeft + 'px' }" />
  </th>
</template>

<script>
export default {
  name: "DataHeaderCell",
  props: {
    column: Object,
    index: Number,
    sorted: Boolean,
    sortDir: Number,
  },
  emits: ["sort", "remove", "drag-start", "drag-over", "drag-end"],
  data() {
    return {
      dragging: false,
      dragLineLeft: 0,
      dragLineVisible: false,
    };
  },
  methods: {
    onSort() {
      this.$emit("sort");
    },
    onRemove() {
      this.$emit("remove");
    },
    onDragStart(e) {
      this.dragging = true;
      this.$emit("drag-start", e);
      e.dataTransfer.effectAllowed = "move";
      e.dataTransfer.setData("text/plain", this.index);
      e.dataTransfer.setDragImage(new Image(), 0, 0);
    },
    onDragOver(e) {
      e.preventDefault();
      this.dragLineVisible = true;
      this.dragLineLeft = this.calculateDragLineLeft(e);
      this.$emit("drag-over", e);
    },
    onDragEnd(e) {
      this.dragging = false;
      this.dragLineVisible = false;
      this.$emit("drag-end", e);
    },
    calculateDragLineLeft(e) {
      const th = e.currentTarget;
      const rect = th.getBoundingClientRect();
      const offsetX = e.clientX - rect.left;
      if (offsetX < rect.width / 2) return rect.left + window.pageXOffset;
      else return rect.right + window.pageXOffset;
    },
  },
};
</script>

<style scoped>
th {
  position: relative;
  user-select: none;
  cursor: pointer;
}
.close-btn {
  margin-left: 6px;
  background: transparent;
  border: none;
  font-size: 1em;
  line-height: 1;
  cursor: pointer;
  color: #999;
  transition: color 0.3s;
  position: absolute;
  top: 50%;
  right: 4px;
  transform: translateY(-50%);
}
.close-btn:hover {
  color: #f5222d;
}
.drag-indicator {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 3px;
  background-color: #1890ff;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.3);
  pointer-events: none;
  z-index: 20;
  transition: left 0.3s ease;
}
.sorted.sort-asc::after {
  content: "▲";
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 0.7em;
  color: #1890ff;
}
.sorted.sort-desc::after {
  content: "▼";
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  font-size: 0.7em;
  color: #1890ff;
}
</style>