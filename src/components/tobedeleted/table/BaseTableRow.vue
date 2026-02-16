
<template>
  <tr
    class="base-table-row"
    :class="{ hovered: isHovered, selected: isSelected }"
    @mouseenter="isHovered = true"
    @mouseleave="isHovered = false"
    @click="onRowClick"
  >
    <slot></slot>
  </tr>
</template>

<script>
export default {
  name: "BaseTableRow",
  props: {
    rowData: { type: Object, required: true },
    selectedIds: { type: Array, default: () => [] },
    rowIdKey: { type: String, default: "id" },
  },
  emits: ["row-select"],
  data() {
    return {
      isHovered: false,
    };
  },
  computed: {
    isSelected() {
      const id = this.rowData?.[this.rowIdKey];
      return this.selectedIds.includes(id);
    },
  },
  methods: {
    onRowClick(e) {
      const ctrlPressed = e.ctrlKey || e.metaKey;
      const id = this.rowData?.[this.rowIdKey];
      this.$emit("row-select", { id, ctrlPressed, event: e });
    },
  },
};
</script>

<style scoped>
.base-table-row.hovered {
  background-color: #f3f8ff;
}
.base-table-row.selected {
  background-color: #cce4ff;
}
.base-table-row:hover {
  cursor: pointer;
}
</style>
