<template>
  <div class="app">
    <h1>Vue.js 3 Custom Table Components</h1>
    <p>Example table using DataTable, DataRow, DataHeaderCell, and DataCell components</p>
    
    <!-- View toggle buttons - simplified, only manages state -->
    <div class="view-toggle">
      <button 
        :class="{ active: tableViewMode === 'horizontal' }"
        @click="tableViewMode = 'horizontal'"
      >
        Horizontal View
      </button>
      <button 
        :class="{ active: tableViewMode === 'vertical' }"
        @click="tableViewMode = 'vertical'"
      >
        Vertical View (Transposed)
      </button>
    </div>

    <!-- DataTable receives viewMode prop and handles all view logic -->
    <DataTable :viewMode="tableViewMode">
      <!-- Header and body slots only for horizontal view data -->
      <template #header>
        <DataRow>
          <DataHeaderCell 
            v-for="header in tableHeaders" 
            :key="header.id"
            :sortable="header.sortable"
          >
            {{ header.label }}
          </DataHeaderCell>
        </DataRow>
      </template>
      
      <template #body>
        <DataRow v-for="item in tableData" :key="item.id">
          <DataCell 
            v-for="header in tableHeaders" 
            :key="`${item.id}-${header.id}`"
            :emphasized="header.id === 1"
          >
            {{ formatCellValue(item, header.dataKey) }}
          </DataCell>
        </DataRow>
      </template>

      <!-- Vertical (Transposed) view - headers as first column, rows as columns -->
      <template #verticalBody>
        <table class="transposed-table">
          <tbody>
            <tr class="field-header-row">
              <th class="header-column">Field</th>
              <th v-for="item in tableData" :key="`col-${item.id}`" class="data-column">
                {{ item.name }}
              </th>
            </tr>
            <tr v-for="header in tableHeaders" :key="`row-${header.id}`">
              <td class="header-column"><strong>{{ header.label }}</strong></td>
              <td v-for="item in tableData" :key="`${item.id}-${header.id}`" class="data-column">
                {{ formatCellValue(item, header.dataKey) }}
              </td>
            </tr>
          </tbody>
        </table>
      </template>
    </DataTable>
  </div>
</template>

<script>
import DataTable from './DataTable.vue'
import DataRow from './DataRow.vue'
import DataHeaderCell from './DataHeaderCell.vue'
import DataCell from './DataCell.vue'

export default {
  name: 'ExampleTable',
  components: {
    DataTable,
    DataRow,
    DataHeaderCell,
    DataCell
  },
  data() {
    return {
      tableViewMode: 'horizontal',
      tableHeaders: [
        { id: 1, label: 'Product Name', sortable: true, dataKey: 'name' },
        { id: 2, label: 'Category', sortable: true, dataKey: 'category' },
        { id: 3, label: 'Price', sortable: true, dataKey: 'price' },
        { id: 4, label: 'Stock', sortable: false, dataKey: 'stock' }
      ],
      tableData: [
        { id: 1, name: 'Laptop', category: 'Electronics', price: 999, stock: 15 },
        { id: 2, name: 'Mouse', category: 'Accessories', price: 29, stock: 50 },
        { id: 3, name: 'Keyboard', category: 'Accessories', price: 79, stock: 32 },
        { id: 4, name: 'Monitor', category: 'Electronics', price: 299, stock: 8 },
        { id: 5, name: 'USB Cable', category: 'Accessories', price: 9, stock: 100 }
      ]
    }
  },
  methods: {
    formatCellValue(item, dataKey) {
      const value = item[dataKey]
      if (dataKey === 'price') {
        return `$${value}`
      }
      return value
    }
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
  background-color: #f3f4f6;
}

.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
}

.app h1 {
  color: #1f2937;
  margin-bottom: 0.5rem;
}

.app p {
  color: #6b7280;
  margin-bottom: 2rem;
}

.view-toggle {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 2rem;
}

.view-toggle button {
  padding: 0.5rem 1rem;
  border: 2px solid #d1d5db;
  background-color: #ffffff;
  color: #6b7280;
  border-radius: 0.375rem;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.2s ease;
}

.view-toggle button:hover {
  border-color: #3b82f6;
  color: #3b82f6;
}

.view-toggle button.active {
  background-color: #3b82f6;
  border-color: #3b82f6;
  color: #ffffff;
}

/* Transposed table styling */
.transposed-table {
  width: 100%;
  border-collapse: collapse;
  background-color: #ffffff;
}

.transposed-table thead {
  background-color: #f3f4f6;
  border-bottom: 2px solid #e5e7eb;
}

.transposed-table th,
.transposed-table td {
  border: 1px solid #e5e7eb;
  padding: 0.75rem;
  text-align: left;
}

.transposed-table th {
  font-weight: 600;
  color: #374151;
  background-color: #f3f4f6;
}

.transposed-table .header-column {
  font-weight: 600;
  background-color: #f3f4f6;
  min-width: 150px;
}

.transposed-table .data-column {
  color: #6b7280;
}

.transposed-table tbody tr:hover {
  background-color: #f9fafb;
}
</style>
