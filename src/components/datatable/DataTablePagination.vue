<template>
  <div class="data-table-pagination-wrapper">
    <slot :paginatedRows="paginatedRows"></slot>
    <div class="pagination">
      <div class="pagination-left">
        <label>
          Rows per page:
          <select :value="recordsPerPage" @change="onPerPageChange">
            <option v-for="opt in perPageOptions" :key="opt" :value="opt">
              {{ opt }}
            </option>
          </select>
        </label>
        <span class="total-records">Total: {{ totalRecords }}</span>
      </div>
      <div class="pagination-right">
        <button :disabled="currentPage <= 1" @click="goToPage(1)">
          &laquo;
        </button>
        <button :disabled="currentPage <= 1" @click="goToPage(currentPage - 1)">
          &lsaquo;
        </button>
        <span class="page-info">Page {{ currentPage }} of {{ totalPages }}</span>
        <button :disabled="currentPage >= totalPages" @click="goToPage(currentPage + 1)">
          &rsaquo;
        </button>
        <button :disabled="currentPage >= totalPages" @click="goToPage(totalPages)">
          &raquo;
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "DataTablePagination",
  props: {
    rows: {
      type: Array,
      required: true,
    },
    perPageOptions: {
      type: Array,
      default: () => [5, 10, 15, 25],
    },
    defaultPerPage: {
      type: Number,
      default: 5,
    },
  },
  data() {
    return {
      currentPage: 1,
      recordsPerPage: this.defaultPerPage,
    };
  },
  computed: {
    totalRecords() {
      return this.rows.length;
    },
    totalPages() {
      return Math.ceil(this.totalRecords / this.recordsPerPage) || 1;
    },
    paginatedRows() {
      const start = (this.currentPage - 1) * this.recordsPerPage;
      return this.rows.slice(start, start + this.recordsPerPage);
    },
  },
  watch: {
    totalPages(newTotal) {
      if (this.currentPage > newTotal) {
        this.currentPage = newTotal;
      }
    },
  },
  methods: {
    goToPage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    },
    onPerPageChange(event) {
      this.recordsPerPage = Number(event.target.value);
      this.currentPage = 1;
    },
  },
};
</script>

<style scoped>
.pagination {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  margin-top: 8px;
  font-family: Arial, sans-serif;
  font-size: 14px;
}

.pagination-left {
  display: flex;
  align-items: center;
  gap: 16px;
}

.pagination-left label {
  display: flex;
  align-items: center;
  gap: 6px;
}

.pagination-left select {
  padding: 4px 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.total-records {
  color: #666;
}

.pagination-right {
  display: flex;
  align-items: center;
  gap: 4px;
}

.pagination-right button {
  padding: 4px 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background: #fff;
  cursor: pointer;
  font-size: 14px;
}

.pagination-right button:hover:not(:disabled) {
  background: #f0f0f0;
}

.pagination-right button:disabled {
  cursor: not-allowed;
  opacity: 0.4;
}

.page-info {
  margin: 0 8px;
  white-space: nowrap;
}
</style>
