<template>
  <div class="base-table-wrapper">
    <table class="base-table">
      <thead>
        <slot name="header" :onDragStart="onDragStart" :onDragOver="onDragOver" :onDrop="onDrop" />
      </thead>
      <tbody>
        <slot />
      </tbody>
      <tfoot>
        <slot name="footer" />
      </tfoot>
    </table>
  </div>
</template>

<script>
export default {
  name: "BaseTable",
  emits: ["reorder-columns"],
  data() {
    return {
      dragStartIndex: null,
    };
  },
  methods: {
    onDragStart(index) {
      this.dragStartIndex = index;
    },
    onDragOver(event) {
      event.preventDefault();
      event.dataTransfer.dropEffect = "move";
    },
    onDrop(event, index) {
      this.$emit("reorder-columns", { from: this.dragStartIndex, to: index });
      this.dragStartIndex = null;
    },
  },
};
</script>

<style scoped>
.base-table-wrapper {
  overflow-x: auto;
}
.base-table {
  border-collapse: collapse;
  width: 100%;
  table-layout: fixed;
}
.base-table th,
.base-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
  min-width: 100px;
}
</style>
