<template>
  <td @click="onClick">
    <slot />
  </td>
</template>

<script>
export default {
  name: "DataCell",
  props: {
    row: Object,
    column: Object,
  },
  emits: ["click", "cell-event"],
  methods: {
    onClick(event) {
      this.$emit("click", event);
      this.$emit("cell-event", "click", this.row, this.column, event);
    },
  },
};
</script>