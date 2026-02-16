<template>
  <div
    class="data-table"
    :class="isHorizontal ? 'horizontal' : 'vertical'"
  >
    <!-- HORIZONTAL VIEW -->
    <template v-if="isHorizontal">
      <div class="data-table-header">
        <slot name="header"></slot>
      </div>

      <div class="data-table-body">
        <slot name="body"></slot>
      </div>
    </template>

    <!-- VERTICAL (TRANSPOSE / EXCEL STYLE) VIEW -->
    <template v-else>
      <div class="vertical-container">

        <div
          class="vertical-row"
          v-for="(header, hIndex) in tableHeaders"
          :key="hIndex"
        >
          <!-- Header cell (appears once only) -->
          <div class="vertical-header">
            {{ header.label }}
          </div>

          <!-- All values under this header -->
          <div
            class="vertical-cell"
            v-for="(row, rIndex) in tableData"
            :key="rIndex"
          >
            {{ row[header.key] }}
          </div>
        </div>

      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: "DataTable",

  props: {
    isHorizontal: { type: Boolean, default: true },
    tableHeaders: { type: Array, required: true },
    tableData: { type: Array, required: true },
  },
};
</script>

<style scoped>
.data-table {
  width: 100%;
  display: flex;
  flex-direction: column;
}

/* ----------------------
   HORIZONTAL LAYOUT
-----------------------*/
.horizontal .data-table-header {
  display: flex;
  font-weight: bold;
}
.horizontal .data-table-body {
  display: flex;
  flex-direction: column;
}

/* ----------------------
   VERTICAL TRANSPOSED VIEW
-----------------------*/
.vertical .vertical-container {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.vertical-row {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.vertical-header {
  font-weight: bold;
  margin-right: 15px;
  min-width: 80px;
}

.vertical-cell {
  margin-right: 15px;
}
</style>
