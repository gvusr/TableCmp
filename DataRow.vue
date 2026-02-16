<!-- DataRow.vue -->
<template>
  <div class="data-row">
    <slot></slot>
  </div>
</template>

<script>
export default {
  name: "DataRow",
};
</script>

<style scoped>
.data-row {
  display: table-row;
}
</style>