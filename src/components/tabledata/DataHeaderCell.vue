<template>
  <th class="data-header-cell" :class="{ 'data-header-cell--sortable': sortable }">
    <div class="data-header-cell-content">
      <slot></slot>
      <span v-if="sortable" class="sort-indicator">
        {{ sortDirection ? (sortDirection === 'asc' ? '▲' : '▼') : '⇅' }}
      </span>
    </div>
  </th>
</template>

<script>
export default {
  name: 'DataHeaderCell',
  props: {
    sortable: {
      type: Boolean,
      default: false
    },
    sortDirection: {
      type: String,
      validator: (value) => ['asc', 'desc', null].includes(value),
      default: null
    }
  },
  data() {
    return {}
  }
}
</script>

<style scoped>
.data-header-cell {
  padding: 0.75rem 1rem;
  text-align: left;
  font-weight: 600;
  color: #1f2937;
  user-select: none;
}

.data-header-cell--sortable {
  cursor: pointer;
}

.data-header-cell-content {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.sort-indicator {
  font-size: 0.75rem;
  opacity: 0.6;
}
</style>
