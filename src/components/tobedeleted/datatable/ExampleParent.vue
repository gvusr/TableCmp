<!-- ExampleParent.vue -->
<template>
  <div>
    <DataTable
      :data="tableData"
      :columns="tableColumns"
      id-key="id"
      :global-search="true"
      :pagination="true"
      :vertical-view="false"
      :row-events="rowEvents"
      :cell-events="cellEvents"
      @row-click="onRowClick"
    />
  </div>
</template>

<script>
import DataTable from "./DataTable.vue";

export default {
  components: { DataTable },
  data() {
    return {
      tableData: [
        { id: 1, name: "Alice", age: 25, email: "alice@example.com" },
        { id: 2, name: "Bob", age: 30, email: "bob@example.com" },
        { id: 3, name: "Charlie", age: 22, email: "charlie@example.com" },
        { id: 4, name: "Diana", age: 28, email: "diana@example.com" },
      ],
      tableColumns: [
        { key: "id", label: "ID", filterable: true },
        { key: "name", label: "Name", filterable: true },
        { key: "age", label: "Age", filterable: true },
        { key: "email", label: "Email", filterable: true },
      ],
      rowEvents: {
        click(row, event) {
          alert(`Row clicked: ID=${row.id}, Name=${row.name}` + event);
        },
        "ctrl-click"(row, event) {
          console.log(`Row selected with Ctrl: ID=${row.id}, Name=${row.name}` + event);
        },
      },
      cellEvents: {
        click(row, column, event) {
          console.log(`Cell clicked: Row ID=${row.id}, Column=${column.key}, Value=${row[column.key]}` + event);
        },
      },
    };
  },
  methods: {
    onRowClick(row) {
      console.log("Row clicked event received in parent:", row);
    },
  },
};
</script>