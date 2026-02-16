<template>
  <div class="pagination">
    <div class="left">
      <label>Rows per page:
        <select v-model.number="perPageLocal" @change="updatePerPage">
          <option v-for="n in [5, 10, 20, 50]" :key="n" :value="n">{{ n }}</option>
        </select>
      </label>
      <span class="total">Total: {{ totalRecords }}</span>
    </div>
    <div class="right">
      <button :disabled="currentPageLocal === 1" @click="prevPage">Prev</button>
      <span>{{ currentPageLocal }}</span>
      <button :disabled="currentPageLocal >= totalPages" @click="nextPage">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "BasePagination",
  props: {
    totalRecords: Number,
    modelValue: Number,
    perPage: Number,
  },
  emits: ["update:modelValue", "update:perPage"],
  computed: {
    totalPages() {
      return Math.ceil(this.totalRecords / this.perPageLocal) || 1;
    },
  },
  data() {
    return {
      perPageLocal: this.perPage || 5,
      currentPageLocal: this.modelValue || 1,
    };
  },
  watch: {
    modelValue(val) {
      this.currentPageLocal = val;
    },
    perPage(val) {
      this.perPageLocal = val;
    },
  },
  methods: {
    updatePerPage() {
      this.$emit("update:perPage", this.perPageLocal);
      this.$emit("update:modelValue", 1);
    },
    prevPage() {
      if (this.currentPageLocal > 1) {
        this.currentPageLocal--;
        this.$emit("update:modelValue", this.currentPageLocal);
      }
    },
    nextPage() {
      if (this.currentPageLocal < this.totalPages) {
        this.currentPageLocal++;
        this.$emit("update:modelValue", this.currentPageLocal);
      }
    },
  },
};
</script>

<style scoped>
.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px;
}
.pagination button {
  margin: 0 4px;
}
</style>
