<template>
  <div class="data-table" :class="{ 'vertical-view': verticalView }">
    <div v-if="globalSearch" class="global-search">
      <input
        type="text"
        v-model="globalSearchTerm"
        @input="onGlobalSearch"
        placeholder="Global Search..."
      />
      <button @click="resetFilters">Reset Filters</button>
    </div>

    <table ref="tableEl" :class="{ 'horizontal-view': !verticalView }">
      <!-- Header Rows -->
      <DataRow
        is-header
        :columns="localColumns"
        :sortable="true"
        @sort="onSort"
        @remove-column="removeColumn"
        @drag-column="onDragColumn"
        
        @reset-columns="resetColumns"
        :dragging-index="draggingIndex"
        :drag-over-index="dragOverIndex"
        @drag-start="dragStart"
        @drag-over="dragOver"
        @drag-end="dragEnd"
      >
        <template #default="{ column, index }">
          <DataHeaderCell
            :column="column"
            :index="index"
            :sorted="sortColumn === column.key"
            :sort-dir="sortDir"
            @sort="() => onSort(column.key)"
            @remove="() => removeColumn(column.key)"
            @drag-start="($event) => dragStart($event, index)"
            @drag-over="($event) => dragOver($event, index)"
            @drag-end="dragEnd"
          >
            <template #default>
              {{ column.label }}
            </template>
          </DataHeaderCell>
        </template>
      </DataRow>

      <DataRow
        is-filter
        :columns="columns"
        :filters="columnFilters"
        @filter-change="onColumnFilterChange"
      >
        <template #default="{ column }">
          <DataCell>
            <template v-if="column.filterable">
              <input
                type="text"
                v-model="columnFilters[column.key]"
                @input="() => onColumnFilterChange(column.key, columnFilters[column.key])"
                placeholder="Filter..."
              />
            </template>
          </DataCell>
        </template>
      </DataRow>

      <!-- Body Rows -->
      <DataRow
        v-for="(row) in pagedData"
        :key="row[idKey]"
        :row="row"
        :columns="columns"
        :selected="selectedRows.includes(row[idKey])"
        @row-click="handleRowClick"
        @cell-event="handleCellEvent"
        @cell-click="handleCellClick"
        @row-event="handleRowEvent"
      >
        <template #default="{ column, row }">
          <DataCell
            :row="row"
            :column="column"
            @click="(e) => emitCellEventOrRowEvent(row, column, e)"
          >
            <template #default>
              {{ row[column.key] }}
            </template>
          </DataCell>
        </template>
      </DataRow>
    </table>

    <BasePagination
      v-if="pagination"
      :page="page"
      :total-pages="totalPages"
      :total-records="filteredData.length"
      :records-per-page="recordsPerPage"
      :records-per-page-options="recordsPerPageOptions"
      @page-change="onPageChange"
      @records-per-page-change="onRecordsPerPageChange"
    />
  </div>
</template>

<script>
import DataRow from "./DataRow.vue";
import DataCell from "./DataCell.vue";
import DataHeaderCell from "./DataHeaderCell.vue";
import BasePagination from "./BasePagination.vue";

export default {
  name: "DataTable",
  components: { DataRow, DataCell, DataHeaderCell, BasePagination },
  props: {
    data: { type: Array, required: true },
    columns: { type: Array, required: true },
    idKey: { type: String, default: "id" },
    horizontalView: { type: Boolean, default: true },
    verticalView: { type: Boolean, default: false },
    globalSearch: { type: Boolean, default: false },
    pagination: { type: Boolean, default: false },
    recordsPerPageOptions: {
      type: Array,
      default: () => [5, 10, 25, 50],
    },
    defaultRecordsPerPage: { type: Number, default: 10 },
    rowEvents: { type: Object, default: () => ({}) },
    cellEvents: { type: Object, default: () => ({}) },
  },
  data() {
    return {
      sortColumn: null,
      sortDir: 1,
      globalSearchTerm: "",
      columnFilters: {},
      filteredData: [],
      selectedRows: [],
      page: 1,
      recordsPerPage: this.defaultRecordsPerPage,
      // Use local copy for columns to avoid mutating prop directly
      localColumns: JSON.parse(JSON.stringify(this.columns)),
      initialColumns: JSON.parse(JSON.stringify(this.columns)),
      draggingIndex: null,
      dragOverIndex: null,
    };
  },
  watch: {
    data: {
      immediate: true,
      handler(val) {
        this.filteredData = val.slice();
        this.applyFilters && this.applyFilters();
      },
    },
    columns: {
      immediate: true,
      handler(newCols) {
        this.localColumns = JSON.parse(JSON.stringify(newCols));
        this.initialColumns = JSON.parse(JSON.stringify(newCols));
        // Optionally reset filters on columns change
        this.resetFilters();
      },
    },
  },
  computed: {
    // Use localColumns instead of columns everywhere
    sortedData() {
      if (!this.sortColumn) return this.filteredData;
      const sorted = [...this.filteredData];
      sorted.sort((a, b) => {
        if (a[this.sortColumn] === b[this.sortColumn]) return 0;
        if (a[this.sortColumn] > b[this.sortColumn]) return this.sortDir;
        return -this.sortDir;
      });
      return sorted;
    },
    filteredDataComputed() {
      return this.sortedData.filter((row) => {
        for (const key in this.columnFilters) {
          if (!this.columnFilters[key]) continue;
          const val = row[key] ? String(row[key]).toLowerCase() : "";
          if (!val.includes(this.columnFilters[key].toLowerCase())) return false;
        }
        return true;
      });
    },
    finalData() {
      if (!this.globalSearchTerm) return this.filteredDataComputed;
      const term = this.globalSearchTerm.toLowerCase();
      return this.filteredDataComputed.filter((row) =>
        this.localColumns.some((col) => {
          const val = row[col.key];
          if (!val) return false;
          return String(val).toLowerCase().includes(term);
        })
      );
    },
    pagedData() {
      if (!this.pagination) return this.finalData;
      const start = (this.page - 1) * this.recordsPerPage;
      return this.finalData.slice(start, start + this.recordsPerPage);
    },
    totalPages() {
      if (!this.pagination) return 1;
      return Math.ceil(this.finalData.length / this.recordsPerPage) || 1;
    },
  },
  methods: {
    onSort(columnKey) {
      if (this.sortColumn === columnKey) {
        this.sortDir = -this.sortDir;
      } else {
        this.sortColumn = columnKey;
        this.sortDir = 1;
      }
    },
    onGlobalSearch() {
      this.page = 1;
    },
    onColumnFilterChange(columnKey, value) {
      this.$set(this.columnFilters, columnKey, value);
      this.page = 1;
    },
    resetFilters() {
      this.globalSearchTerm = "";
      Object.keys(this.columnFilters).forEach((k) => (this.columnFilters[k] = ""));
      this.localColumns = JSON.parse(JSON.stringify(this.initialColumns));
      this.page = 1;
      this.sortColumn = null;
      this.sortDir = 1;
    },
    handleRowClick(row, event) {
      if (event.ctrlKey || event.metaKey) {
        const id = row[this.idKey];
        if (this.selectedRows.includes(id)) {
          this.selectedRows = this.selectedRows.filter((r) => r !== id);
        } else {
          this.selectedRows.push(id);
        }
      } else {
        this.$emit("row-click", row);
      }
    },
    emitCellEventOrRowEvent(row, column, event) {
      if (this.cellEvents && this.cellEvents.click) {
        this.cellEvents.click(row, column, event);
      } else if (this.rowEvents && this.rowEvents.click) {
        this.rowEvents.click(row, event);
      }
    },
    handleCellEvent(eventName, ...args) {
      if (this.cellEvents && this.cellEvents[eventName]) {
        this.cellEvents[eventName](...args);
      } else if (this.rowEvents && this.rowEvents[eventName]) {
        this.rowEvents[eventName](...args);
      }
    },
    handleCellClick(row, column, event) {
      this.emitCellEventOrRowEvent(row, column, event);
    },
    handleRowEvent(eventName, ...args) {
      if (this.rowEvents && this.rowEvents[eventName]) {
        this.rowEvents[eventName](...args);
      }
    },
    onPageChange(newPage) {
      this.page = newPage;
    },
    onRecordsPerPageChange(newCount) {
      this.recordsPerPage = newCount;
      this.page = 1;
    },
    removeColumn(key) {
      this.localColumns = this.localColumns.filter((c) => c.key !== key);
      this.$delete(this.columnFilters, key);
    },
    resetColumns() {
      this.localColumns = JSON.parse(JSON.stringify(this.initialColumns));
      this.columnFilters = {};
      this.initialColumns.forEach((col) => {
        this.$set(this.columnFilters, col.key, "");
      });
    },
    dragStart(e, index) {
      this.draggingIndex = index;
    },
    dragOver(e, index) {
      if (index !== this.draggingIndex) {
        this.dragOverIndex = index;
      }
    },
    dragEnd() {
      if (
        this.draggingIndex !== null &&
        this.dragOverIndex !== null &&
        this.draggingIndex !== this.dragOverIndex
      ) {
        const cols = [...this.localColumns];
        const dragged = cols.splice(this.draggingIndex, 1)[0];
        cols.splice(this.dragOverIndex, 0, dragged);
        this.localColumns = cols;
      }
      this.draggingIndex = null;
      this.dragOverIndex = null;
    },
    onDragColumn(fromIndex, toIndex) {
      console.log(fromIndex + " :: "+ toIndex);
    },
    onDragEnd() {},
  },
};
</script>

<style scoped>
.data-table {
  width: 100%;
  overflow-x: auto;
  --header-bg: #f0f2f5;
  --hover-bg: #e6f7ff;
  --selected-bg: #bae7ff;
  --border-color: #d9d9d9;
  --drag-indicator-shadow: 0 0 8px rgba(0, 0, 0, 0.3);
  --drag-indicator-color: #1890ff;
}
.data-table table {
  border-collapse: collapse;
  width: 100%;
  table-layout: fixed;
  user-select: none;
}
.data-table.vertical-view table {
  writing-mode: vertical-lr;
  text-orientation: mixed;
}
.global-search {
  margin-bottom: 8px;
  display: flex;
  gap: 8px;
  align-items: center;
}
.global-search input {
  flex-grow: 1;
  padding: 6px 8px;
  font-size: 1em;
}
tr.row-hover {
  background-color: var(--hover-bg);
  transition: background-color 0.3s ease;
}
tr.row-selected {
  background-color: var(--selected-bg);
}
th {
  background-color: var(--header-bg);
  color: #595959;
  font-weight: 600;
  padding: 10px 12px;
  border-bottom: 2px solid var(--border-color);
  text-align: left;
  user-select: none;
  position: relative;
  white-space: nowrap;
  cursor: pointer;
}
td {
  padding: 10px 12px;
  border-bottom: 1px solid #f0f0f0;
  color: #262626;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
th.sorted.sort-asc::after,
th.sorted.sort-desc::after {
  font-size: 0.7em;
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--drag-indicator-color);
  user-select: none;
}
th.sorted.sort-asc::after {
  content: "▲";
}
th.sorted.sort-desc::after {
  content: "▼";
}
.close-btn {
  margin-left: 6px;
  background: transparent;
  border: none;
  font-size: 1em;
  line-height: 1;
  cursor: pointer;
  color: #999;
  transition: color 0.3s;
  position: absolute;
  top: 50%;
  right: 4px;
  transform: translateY(-50%);
}
.close-btn:hover {
  color: #f5222d;
}
.drag-indicator {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 3px;
  background-color: var(--drag-indicator-color);
  box-shadow: var(--drag-indicator-shadow);
  pointer-events: none;
  z-index: 20;
  transition: left 0.3s ease;
}
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