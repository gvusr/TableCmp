<template>
  <td @click="onCellClick">
    <slot>{{ cellData }}</slot>
  </td>
</template>

<script>
export default {
  name: "BaseTableCell",
  props: {
    cellData: [String, Number],
    rowData: Object,
    column: Object,
    cellEvents: { type: Object, default: () => ({}) },
  },
  methods: {
    onCellClick(e) {
      if (this.cellEvents.click)
        this.cellEvents.click(this.cellData, this.rowData, this.column, e);
    },
  },
};
</script>
