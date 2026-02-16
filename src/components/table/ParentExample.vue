<template>
  <div>
    <button @click="toggleView">
      Switch to {{ isHorizontal ? 'Vertical' : 'Horizontal' }} View
    </button>

    <DataTable
      :isHorizontal="isHorizontal"
      :tableHeaders="tableHeaders"
      :tableData="tableData"
    >
      <template #header>
        <DataRow>
          <DataHeaderCell
            v-for="(header, index) in tableHeaders"
            :key="index"
          >
            {{ header.label }}
          </DataHeaderCell>
        </DataRow>
      </template>

      <template #body>
        <DataRow
          v-for="(row, rowIndex) in tableData"
          :key="rowIndex"
        >
          <DataCell
            v-for="(header, colIndex) in tableHeaders"
            :key="colIndex"
          >
            {{ row[header.key] }}
          </DataCell>
        </DataRow>
      </template>
    </DataTable>
  </div>
</template>

<script>
import DataTable from "./DataTable.vue";
import DataRow from "./DataRow.vue";
import DataHeaderCell from "./DataHeaderCell.vue";
import DataCell from "./DataCell.vue";

export default {
  name: "ParentExample",
  components: {
    DataTable,
    DataRow,
    DataHeaderCell,
    DataCell,
  },

  data() {
    return {
      isHorizontal: true,

      tableHeaders: [
        { label: "ID", key: "id" },
        { label: "Name", key: "name" },
        { label: "Age", key: "age" },
      ],

      tableData: [
        { id: 1, name: "John", age: 24 },
        { id: 2, name: "Alice", age: 30 },
        { id: 3, name: "Michael", age: 28 },
      ],
    };
  },

  methods: {
    toggleView() {
      this.isHorizontal = !this.isHorizontal;
    },
  },
};
</script>
