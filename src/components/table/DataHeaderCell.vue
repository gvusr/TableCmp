<template>
  <th class="data-header-cell">
    <slot></slot>
  </th>
</template>

<script>
export default {
  name: "DataHeaderCell",
};
</script>

<style scoped>
.data-header-cell {
  font-weight: bold;
}
</style>
