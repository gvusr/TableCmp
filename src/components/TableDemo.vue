<template>
  <div class="table-demo">
    <div class="controls">
      <label>
        <input type="checkbox" v-model="isHorizontal" />
        Horizontal View
      </label>
    </div>

    <DataTable :is-horizontal="isHorizontal">
      <!-- Header Row -->
      <DataRow class="header-row">
        <DataHeaderCell label="ID">ID</DataHeaderCell>
        <DataHeaderCell label="Name">Name</DataHeaderCell>
        <DataHeaderCell label="Email">Email</DataHeaderCell>
        <DataHeaderCell label="Status">Status</DataHeaderCell>
      </DataRow>

      <!-- Data Rows -->
      <DataRow v-for="row in tableData" :key="row.id" :row-data="row">
        <DataCell :value="row.id">{{ row.id }}</DataCell>
        <DataCell :value="row.name">{{ row.name }}</DataCell>
        <DataCell :value="row.email">{{ row.email }}</DataCell>
        <DataCell :value="row.status">{{ row.status }}</DataCell>
      </DataRow>
    </DataTable>
  </div>
</template>

<script >
import { defineComponent } from 'vue';
import DataTable from './DataTable.vue';
import DataRow from './DataRow.vue';
import DataHeaderCell from './DataHeaderCell.vue';
import DataCell from './DataCell.vue';



export default defineComponent({
  name: 'TableDemo',
  components: {
    DataTable,
    DataRow,
    DataHeaderCell,
    DataCell
  },
  data() {
    return {
      isHorizontal: true,
      tableData: [
        { id: 1, name: 'John Doe', email: 'john@example.com', status: 'Active' },
        { id: 2, name: 'Jane Smith', email: 'jane@example.com', status: 'Inactive' },
        { id: 3, name: 'Bob Johnson', email: 'bob@example.com', status: 'Active' },
        { id: 4, name: 'Alice Brown', email: 'alice@example.com', status: 'Active' }
      ]
    };
  }
});
</script>

<style scoped>
.table-demo {
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

.controls {
  margin-bottom: 20px;
}

.controls label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
}

.controls input[type="checkbox"] {
  cursor: pointer;
}

.header-row {
  background-color: #f0f0f0;
}
</style>
