<template>
  <div class="pagination">
    <button @click="prev" :disabled="page <= 1">Prev</button>
    <span>Page {{ page }} of {{ totalPages }}</span>
    <button @click="next" :disabled="page >= totalPages">Next</button>
    <select :value="recordsPerPage" @change="onPerPageChange">
      <option v-for="opt in recordsPerPageOptions" :key="opt" :value="opt">
        {{ opt }} per page
      </option>
    </select>
    <span>Total Records: {{ totalRecords }}</span>
  </div>
</template>

<script>
export default {
  name: "BasePagination",
  props: {
    page: Number,
    totalPages: Number,
    totalRecords: Number,
    recordsPerPage: Number,
    recordsPerPageOptions: Array,
  },
  emits: ["page-change", "records-per-page-change"],
  methods: {
    prev() {
      if (this.page > 1) this.$emit("page-change", this.page - 1);
    },
    next() {
      if (this.page < this.totalPages) this.$emit("page-change", this.page + 1);
    },
    onPerPageChange(event) {
      this.$emit("records-per-page-change", Number(event.target.value));
    },
  },
};
</script>

<style scoped>
.pagination {
  margin-top: 12px;
  display: flex;
  gap: 12px;
  justify-content: center;
  align-items: center;
}
.pagination button {
  padding: 4px 12px;
  cursor: pointer;
}
.pagination button[disabled] {
  cursor: not-allowed;
  opacity: 0.5;
}
.pagination select {
  padding: 4px 8px;
}
</style>