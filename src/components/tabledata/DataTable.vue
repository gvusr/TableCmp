<template>
  <div class="data-table-wrapper" :class="{ 'vertical-view': isVerticalView }">
    <!-- Horizontal view -->
    <table v-if="!isVerticalView" class="data-table">
      <thead>
        <slot name="header"></slot>
      </thead>
      <tbody>
        <slot name="body"></slot>
      </tbody>
    </table>
    
    <!-- Vertical (Transposed) view -->
    <div v-else class="vertical-table">
      <slot name="verticalBody"></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DataTable',
  props: {
    viewMode: {
      type: String,
      default: 'horizontal',
      validator(value) {
        return ['horizontal', 'vertical'].includes(value)
      }
    }
  },
  computed: {
    isVerticalView() {
      return this.viewMode === 'vertical'
    }
  }
}
</script>

<style scoped>
.data-table-wrapper {
  width: 100%;
  overflow-x: auto;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
  background-color: #ffffff;
}

.data-table thead {
  background-color: #f3f4f6;
  border-bottom: 2px solid #e5e7eb;
}

.data-table-wrapper.vertical-view {
  overflow-x: auto;
  background-color: transparent;
  border: none;
}

/* Transposed table layout - headers as first column, rows as columns */
.vertical-table {
  width: 100%;
  border: 1px solid #e5e7eb;
  border-radius: 0.5rem;
  background-color: #ffffff;
  overflow-x: auto;
}

.vertical-table table {
  width: 100%;
  border-collapse: collapse;
}

.vertical-table thead {
  background-color: #f3f4f6;
}

.vertical-table th {
  border: 1px solid #e5e7eb;
  padding: 0.75rem;
  text-align: left;
  font-weight: 600;
  color: #374151;
  background-color: #f3f4f6;
}

.vertical-table td {
  border: 1px solid #e5e7eb;
  padding: 0.75rem;
  color: #6b7280;
}

.vertical-table tr:hover {
  background-color: #f9fafb;
}
</style>
