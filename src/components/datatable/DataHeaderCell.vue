<!-- DataHeaderCell.vue -->
<template>
  <div
    class="data-header-cell"
    :style="{ width: width + 'px' }"
    ref="headerCell"
    @mousedown.prevent="onMouseDown"
  >
    <span class="header-content">{{ label }}</span>
    <button class="close-btn" @click.stop.prevent="removeColumn">×</button>
  </div>
</template>

<script>
export default {
  name: "DataHeaderCell",
  props: {
    label: { type: String, required: true },
    index: { type: Number, required: true },
    width: { type: Number, required: true },
    columnsCount: { type: Number, required: true },
    columnsOrder: { type: Array, required: true },
  },
  emits: ["remove-column", "column-reorder", "drag-start", "drag-move", "drag-end"],
  data() {
    return {
      dragging: false,
      dragStartX: 0,
      dragCurrentX: 0,
      dragIndicatorStyle: null,
      draggedIndex: null,
      tableRect: null,
      columnsWidths: [],
      mouseMoved: false,
    };
  },
  mounted() {
    this.calculateColumnsWidths();
    window.addEventListener("resize", this.calculateColumnsWidths);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.calculateColumnsWidths);
  },
  methods: {
    removeColumn() {
      this.$emit("remove-column", this.index);
    },
    onMouseDown(event) {
      if (event.button !== 0) return;
      this.mouseMoved = false;
      this.dragStartX = event.clientX;
      this.draggedIndex = this.index;
      this.tableRect = this.$el.closest(".data-table").getBoundingClientRect();
      this.calculateColumnsWidths();

      window.addEventListener("mousemove", this.onMouseMove);
      window.addEventListener("mouseup", this.onMouseUp);

      // Delay starting drag until mouse moves enough to avoid conflict with clicks (like close button)
    },
    onMouseMove(event) {
      const moveThreshold = 5; // pixels
      if (!this.dragging) {
        if (Math.abs(event.clientX - this.dragStartX) > moveThreshold) {
          this.dragging = true;
          this.$emit("drag-start");
        } else {
          return; // don't start drag yet
        }
      }

      if (this.dragging) {
        this.dragCurrentX = event.clientX;
        this.updateDragIndicator(this.dragCurrentX);
        this.$emit("drag-move", this.dragIndicatorStyle);
      }
    },
    onMouseUp() {
      window.removeEventListener("mousemove", this.onMouseMove);
      window.removeEventListener("mouseup", this.onMouseUp);

      if (!this.dragging) {
        // Mouse up without drag: treat as click, do nothing here (close button handles its own)
        return;
      }

      this.dragging = false;

      const relativeX = this.dragCurrentX - this.tableRect.left;
      let newIndex = 0;
      let acc = 0;
      for (let i = 0; i < this.columnsWidths.length; i++) {
        acc += this.columnsWidths[i];
        if (relativeX < acc) {
          newIndex = i;
          break;
        }
        if (i === this.columnsWidths.length - 1) {
          newIndex = this.columnsWidths.length;
        }
      }

      if (newIndex > this.draggedIndex) newIndex--;

      if (newIndex !== this.draggedIndex) {
        this.$emit("column-reorder", { from: this.draggedIndex, to: newIndex });
      }

      this.dragIndicatorStyle = null;
      this.$emit("drag-end");
    },
    calculateColumnsWidths() {
      const table = this.$el.closest(".data-table");
      if (!table) return;
      const headerCells = table.querySelectorAll(".data-header-cell");
      this.columnsWidths = Array.from(headerCells).map((cell) =>
        Math.round(cell.getBoundingClientRect().width)
      );
    },
    updateDragIndicator(clientX) {
      if (!this.tableRect) return;
      const relativeX = clientX - this.tableRect.left;

      let acc = 0;
      const boundaries = [0];
      for (let w of this.columnsWidths) {
        acc += w;
        boundaries.push(acc);
      }

      let closestBoundary = boundaries[0];
      let minDist = Math.abs(relativeX - closestBoundary);
      for (let b of boundaries) {
        const dist = Math.abs(relativeX - b);
        if (dist < minDist) {
          minDist = dist;
          closestBoundary = b;
        }
      }

      const rows = this.$el.closest(".data-table").querySelectorAll(".data-row");
      if (!rows.length) return;
      const firstRowTop = rows[0].getBoundingClientRect().top;
      const lastRowBottom = rows[rows.length - 1].getBoundingClientRect().bottom;
      const top = firstRowTop - this.tableRect.top;
      const height = lastRowBottom - firstRowTop;

      this.dragIndicatorStyle = {
        left: closestBoundary + "px",
        top: top + "px",
        height: height + "px",
      };
    },
  },
};
</script>

<style scoped>
.data-header-cell {
  position: relative;
  display: table-cell;
  user-select: none;
  border: 1px solid #ccc;
  background-color: #f5f5f5;
  box-sizing: border-box;
  height: 36px;
  white-space: nowrap;
  padding: 8px 12px;
  cursor: move;
}

.header-content {
  display: inline-block;
  font-weight: bold;
  user-select: none;
  width: 100%;
  overflow: hidden;
  text-overflow: ellipsis;
}

.close-btn {
  position: absolute;
  right: 4px;
  top: 50%;
  transform: translateY(-50%);
  border: none;
  background: transparent;
  font-size: 14px;
  line-height: 1;
  cursor: pointer;
  color: #999;
  user-select: none;
  padding: 0 4px;
  transition: color 0.2s ease;
  z-index: 10;
}

.close-btn:hover {
  color: #ff4d4f;
}
</style>