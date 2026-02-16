<!-- ExampleTable.vue -->
<template>
  <div>
    <div class="controls" style="margin-bottom: 12px;">
      <button @click="toggleView" style="margin-right: 12px;">
        Switch to {{ tableView === "horizontal" ? "Vertical" : "Horizontal" }} View
      </button>
      <button @click="resetColumns">Reset Columns</button>
    </div>

    <DataTable
      :view="tableView"
      :dragging="dragging"
      :dragIndicatorStyle="dragIndicatorStyle"
      ref="dataTable"
    >
      <template v-if="tableView === 'horizontal'">
        <DataRow>
          <DataHeaderCell
            v-for="(header, index) in currentHeaders"
            :key="header.key"
            :label="header.label"
            :index="index"
            :width="columnWidths[index] || 120"
            :columns-count="currentHeaders.length"
            :columns-order="currentHeaders"
            @remove-column="removeColumn"
            @column-reorder="onColumnReorder"
            @drag-start="onDragStart"
            @drag-move="onDragMove"
            @drag-end="onDragEnd"
          />
        </DataRow>
        <DataRow v-for="(row, rowIndex) in rows" :key="rowIndex">
          <DataCell
            v-for="(header) in currentHeaders"
            :key="header.key"
          >
            {{ row[header.originalIndex] }}
          </DataCell>
        </DataRow>
      </template>

      <template v-else>
        <!-- Vertical view: transpose -->
        <DataRow v-for="(header, colIndex) in currentHeaders" :key="header.key">
          <DataHeaderCell
            :label="header.label"
            :index="colIndex"
            :width="columnWidths[colIndex] || 120"
            :columns-count="currentHeaders.length"
            :columns-order="currentHeaders"
            @remove-column="removeColumn"
            @column-reorder="onColumnReorder"
            @drag-start="onDragStart"
            @drag-move="onDragMove"
            @drag-end="onDragEnd"
          />
          <DataCell v-for="(row, rowIndex) in rows" :key="rowIndex">
            {{ row[header.originalIndex] }}
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
  name: "ExampleTable",
  components: {
    DataTable,
    DataRow,
    DataHeaderCell,
    DataCell,
  },
  data() {
    const initialHeaders = [
      { key: "id", label: "ID", originalIndex: 0 },
      { key: "name", label: "Name", originalIndex: 1 },
      { key: "age", label: "Age", originalIndex: 2 },
      { key: "email", label: "Email", originalIndex: 3 },
      { key: "country", label: "Country", originalIndex: 4 },
      { key: "occupation", label: "Occupation", originalIndex: 5 },
      { key: "department", label: "Department", originalIndex: 6 },
      { key: "startDate", label: "Start Date", originalIndex: 7 },
      { key: "status", label: "Status", originalIndex: 8 },
      { key: "salary", label: "Salary", originalIndex: 9 },
      { key: "phone", label: "Phone", originalIndex: 10 },
    ];
    return {
      tableView: "horizontal",
      initialHeaders,
      currentHeaders: [...initialHeaders],
      rows: [
        [1, "John", 28, "john@example.com", "USA", "Developer", "IT", "2018-04-23", "Active", "$70000", "555-1234"],
        [2, "Jane", 24, "jane@example.com", "Canada", "Designer", "Marketing", "2019-07-12", "Active", "$65000", "555-5678"],
        [3, "Alex", 31, "alex@example.com", "UK", "Manager", "Sales", "2016-03-15", "Inactive", "$90000", "555-8765"],
        [4, "Maria", 27, "maria@example.com", "Australia", "Engineer", "R&D", "2020-01-30", "Active", "$72000", "555-4321"],
        [5, "Sam", 22, "sam@example.com", "New Zealand", "Intern", "IT", "2021-06-01", "Active", "$35000", "555-3456"],
        [6, "Lara", 29, "lara@example.com", "USA", "Analyst", "Finance", "2017-11-20", "Active", "$68000", "555-6543"],
        [7, "Tom", 35, "tom@example.com", "Canada", "Consultant", "Consulting", "2015-05-10", "Inactive", "$85000", "555-7890"],
        [8, "Nina", 30, "nina@example.com", "UK", "HR Manager", "HR", "2014-08-25", "Active", "$78000", "555-0987"],
        [9, "Rick", 26, "rick@example.com", "Australia", "Developer", "IT", "2019-09-17", "Active", "$71000", "555-2345"],
        [10, "Eva", 28, "eva@example.com", "New Zealand", "Designer", "Marketing", "2020-12-05", "Active", "$67000", "555-6789"],
        [11, "Mark", 33, "mark@example.com", "USA", "Support", "Customer Service", "2016-10-11", "Active", "$60000", "555-1122"],
        [12, "Zoe", 27, "zoe@example.com", "Canada", "Engineer", "R&D", "2018-02-28", "Inactive", "$73000", "555-3344"],
        [13, "Paul", 34, "paul@example.com", "UK", "Sales Rep", "Sales", "2017-07-14", "Active", "$64000", "555-5566"],
        [14, "Anna", 25, "anna@example.com", "Australia", "Intern", "Marketing", "2021-03-22", "Active", "$34000", "555-7788"],
        [15, "Luke", 29, "luke@example.com", "New Zealand", "Developer", "IT", "2015-12-03", "Active", "$72000", "555-9900"],
      ],
      dragging: false,
      dragIndicatorStyle: null,
      columnWidths: [],
    };
  },
  watch: {
    currentHeaders() {
      this.$nextTick(() => {
        this.calculateColumnWidths();
      });
    },
  },
  mounted() {
    this.calculateColumnWidths();
    window.addEventListener("resize", this.calculateColumnWidths);
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.calculateColumnWidths);
  },
  methods: {
    toggleView() {
      this.tableView = this.tableView === "horizontal" ? "vertical" : "horizontal";
    },
    resetColumns() {
      this.currentHeaders = [...this.initialHeaders];
      this.dragIndicatorStyle = null;
    },
    removeColumn(index) {
      this.currentHeaders.splice(index, 1);
      this.dragIndicatorStyle = null;
    },
    onColumnReorder({ from, to }) {
      if (from === to || from === null || to === null) return;
      const moved = this.currentHeaders.splice(from, 1)[0];
      let insertIndex = to;
      if (to > from) insertIndex = to;
      else insertIndex = to;
      this.currentHeaders.splice(insertIndex, 0, moved);
      this.dragIndicatorStyle = null;
    },
    calculateColumnWidths() {
      this.$nextTick(() => {
        const table = this.$refs.dataTable.$el;
        const headerCells = table.querySelectorAll(".data-header-cell");
        this.columnWidths = Array.from(headerCells).map((cell) =>
          Math.round(cell.getBoundingClientRect().width)
        );
      });
    },
    onDragStart() {
      this.dragging = true;
    },
    onDragMove(style) {
      this.dragIndicatorStyle = style;
    },
    onDragEnd() {
      this.dragging = false;
      this.dragIndicatorStyle = null;
    },
  },
};
</script>