<template>
  <DataRow v-if="view === 'horizontal'" class="filter-row">
    <DataCell
      v-for="(header, index) in headers"
      :key="'filter-' + header.key"
    >
      <input
        v-if="!skipIndices.includes(index)"
        type="text"
        class="column-filter"
        :placeholder="'Filter ' + header.label"
        :value="filters[header.key] || ''"
        @input="onInput(header.key, $event.target.value)"
      />
    </DataCell>
  </DataRow>
  <!-- Vertical view: renders a single filter DataCell per row (used inside each DataRow) -->
  <DataCell
    v-else
    class="filter-cell"
  >
    <input
      v-if="!skipIndices.includes(columnIndex)"
      type="text"
      class="column-filter"
      :placeholder="'Filter'"
      :value="filters[filterKey] || ''"
      @input="onInput(filterKey, $event.target.value)"
    />
  </DataCell>
</template>

<script>
import DataRow from "./DataRow.vue";
import DataCell from "./DataCell.vue";

export default {
  name: "DataTableFilter",
  components: {
    DataRow,
    DataCell,
  },
  props: {
    view: {
      type: String,
      default: "horizontal",
      validator: (v) => ["horizontal", "vertical"].includes(v),
    },
    headers: {
      type: Array,
      default: () => [],
    },
    filters: {
      type: Object,
      default: () => ({}),
    },
    skipIndices: {
      type: Array,
      default: () => [0],
    },
    // Vertical-only props
    columnIndex: {
      type: Number,
      default: 0,
    },
    filterKey: {
      type: String,
      default: "",
    },
  },
  emits: ["filter-change"],
  methods: {
    onInput(key, value) {
      this.$emit("filter-change", { key, value });
    },
  },
};
</script>

<style scoped>
.column-filter {
  width: 100%;
  padding: 4px 6px;
  border: 1px solid #ccc;
  border-radius: 3px;
  font-size: 12px;
  box-sizing: border-box;
}

.column-filter:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 3px rgba(0, 123, 255, 0.3);
}
</style>
