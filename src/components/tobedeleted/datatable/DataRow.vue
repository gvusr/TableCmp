<template>
  <tr
    :class="{
      'row-hover': hover,
      'row-selected': selected,
      'header-row': isHeader,
      'filter-row': isFilter,
    }"
    :draggable="isHeader"
    @click="onRowClick"
    @mouseenter="hover = true"
    @mouseleave="hover = false"
    @dragstart="handleDragStart"
    @dragover="handleDragOver"
    @drop="handleDrop"
    @dragend="handleDragEnd"
  >
    <slot
      v-for="(column, index) in columns"
      :key="isHeader || isFilter ? index : row[idKey]"
      :column="column"
      :index="index"
      :row="row"
    />
  </tr>
</template>

<script>
export default {
  name: "DataRow",
  props: {
    columns: Array,
    row: Object,
    idKey: { type: String, default: "id" },
    isHeader: Boolean,
    isFilter: Boolean,
    selected: Boolean,
    sortable: Boolean,
    draggingIndex: Number,
    dragOverIndex: Number,
  },
  emits: [
    "sort",
    "remove-column",
    "drag-column",
    "drag-end",
    "reset-columns",
    "row-click",
    "cell-event",
    "cell-click",
    "row-event",
    "filter-change",
    "drag-start",
    "drag-over",
    "drop",
    "drag-end",
  ],
  data() {
    return {
      hover: false,
    };
  },
  methods: {
    onRowClick(event) {
      if (!this.isHeader && !this.isFilter) {
        if (event.ctrlKey || event.metaKey) {
          this.$emit("row-event", "ctrl-click", this.row, event);
        } else {
          this.$emit("row-event", "click", this.row, event);
        }
        this.$emit("row-click", this.row, event);
      }
    },
    handleDragStart(e) {
      if (this.isHeader) this.$emit("drag-start", e);
    },
    handleDragOver(e) {
      if (this.isHeader) {
        e.preventDefault();
        this.$emit("drag-over", e);
      }
    },
    handleDrop(e) {
      if (this.isHeader) {
        e.preventDefault();
        this.$emit("drop", e);
      }
    },
    handleDragEnd(e) {
      if (this.isHeader) this.$emit("drag-end", e);
    },
  },
};
</script>