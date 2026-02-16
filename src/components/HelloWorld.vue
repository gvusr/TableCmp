<template>
  <div>
    <h1>{{ msg }}</h1>
    <ExampleTable></ExampleTable>
  </div>
</template>

<script>
import ExampleTable from './datatable/ExampleTable.vue';
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  components: {
    ExampleTable
  }
}
</script>

<style scoped>
h1 {
  margin: 40px 0 0;
}
</style>
