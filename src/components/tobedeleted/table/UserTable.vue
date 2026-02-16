<template>
  <div class="user-table-container">
    <!-- Global Search + Reset -->
    <div class="top-bar">
      <input v-model="globalSearch" placeholder="Global search..." />
      <button @click="resetFilters">Reset Filters</button>
    </div>

    <BaseTable @reorder-columns="reorderColumns">
      <!-- Header + Top Filters -->
      <template #header="{ onDragStart, onDragOver, onDrop }">
        <tr>
          <BaseTableHeaderCell
            v-for="(col, index) in columns"
            :key="col.key"
            :index="index"
            :column="col"
            :on-drag-start="onDragStart"
            :on-drag-over="onDragOver"
            :on-drop="onDrop"
            @remove-column="removeColumn"
          />
        </tr>
        <tr>
          <th v-for="col in columns" :key="col.key">
            <input v-model="columnFilters[col.key]" placeholder="Filter..." />
          </th>
        </tr>
      </template>

      <!-- Rows -->
      <BaseTableRow
        v-for="user in paginatedFilteredUsers"
        :key="user.id"
        :row-data="user"
        :selected-ids="selectedRowIds"
        row-id-key="id"
        @row-select="onRowSelect"
      >
        <BaseTableCell
          v-for="col in columns"
          :key="col.key"
          :cell-data="user[col.key]"
          :row-data="user"
          :column="col"
          :cell-events="cellEvents"
        />
      </BaseTableRow>

      <!-- Bottom Filters -->
      <template #footer>
        <tr>
          <td v-for="col in columns" :key="col.key">
            <input v-model="bottomFilters[col.key]" placeholder="Filter..." />
          </td>
        </tr>
      </template>
    </BaseTable>

    <!-- Pagination -->
    <BasePagination
      :total-records="filteredUsers.length"
      v-model="currentPage"
      v-model:perPage="perPage"
    />
  </div>
</template>

<script>
import BaseTable from "./BaseTable.vue";
import BaseTableHeaderCell from "./BaseTableHeaderCell.vue";
import BaseTableRow from "./BaseTableRow.vue";
import BaseTableCell from "./BaseTableCell.vue";
import BasePagination from "./BasePagination.vue";

export default {
  name: "UserTable",
  components: {
    BaseTable,
    BaseTableHeaderCell,
    BaseTableRow,
    BaseTableCell,
    BasePagination,
  },
  data() {
    return {
      columns: [
        { key: "id", label: "ID" },
        { key: "name", label: "Name" },
        { key: "age", label: "Age" },
        { key: "country", label: "Country" },
      ],
      users: [
        { id: 1, name: "John", age: 29, country: "India" },
        { id: 2, name: "Sara", age: 24, country: "USA" },
        { id: 3, name: "Raj", age: 35, country: "UK" },
        { id: 4, name: "Amit", age: 31, country: "India" },
        { id: 5, name: "Mary", age: 22, country: "Canada" },
        { id: 6, name: "Liam", age: 27, country: "Australia" },
        { id: 7, name: "Emma", age: 30, country: "France" },
      ],
      globalSearch: "",
      columnFilters: {},
      bottomFilters: {},
      selectedRowIds: [],
      currentPage: 1,
      perPage: 5,
      cellEvents: {
        click: (value, row, column) => console.log("Cell clicked:", column.key, value, row),
      },
    };
  },
  computed: {
    filteredUsers() {
      let result = [...this.users];
      // global search
      if (this.globalSearch) {
        const s = this.globalSearch.toLowerCase();
        result = result.filter((u) =>
          Object.values(u).some((v) => String(v).toLowerCase().includes(s))
        );
      }
      // top filters
      Object.entries(this.columnFilters).forEach(([key, value]) => {
        if (value)
          result = result.filter((u) =>
            String(u[key] || "").toLowerCase().includes(value.toLowerCase())
          );
      });
      // bottom filters
      Object.entries(this.bottomFilters).forEach(([key, value]) => {
        if (value)
          result = result.filter((u) =>
            String(u[key] || "").toLowerCase().includes(value.toLowerCase())
          );
      });
      return result;
    },
    paginatedFilteredUsers() {
      const start = (this.currentPage - 1) * this.perPage;
      return this.filteredUsers.slice(start, start + this.perPage);
    },
  },
  methods: {
    reorderColumns({ from, to }) {
      const moved = this.columns.splice(from, 1)[0];
      this.columns.splice(to, 0, moved);
    },
    removeColumn(index) {
      this.columns.splice(index, 1);
    },
    resetFilters() {
      this.globalSearch = "";
      this.columnFilters = {};
      this.bottomFilters = {};
    },
    onRowSelect({ id, ctrlPressed }) {
      if (ctrlPressed) {
        if (this.selectedRowIds.includes(id)) {
          this.selectedRowIds = this.selectedRowIds.filter((x) => x !== id);
        } else {
          this.selectedRowIds = [...this.selectedRowIds, id];
        }
      } else {
        this.selectedRowIds = [id];
      }
    },
  },
};
</script>

<style scoped>
.user-table-container {
  font-family: Arial, sans-serif;
}
.top-bar {
  margin-bottom: 10px;
  display: flex;
  gap: 10px;
}
input {
  padding: 4px;
  width: 100%;
  box-sizing: border-box;
}
</style>
