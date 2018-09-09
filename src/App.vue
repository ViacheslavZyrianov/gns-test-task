<template>
  <div id="app">
    <data-table />
  </div>
</template>

<script>
import DataTable from './components/DataTable.vue'

export default {
  name: 'app',
  components: {
    DataTable
  }
}
</script>

<style lang="scss">
@import 'assets/scss/normalize.css';
@import 'assets/scss/popup.scss';

#app {
  font-family: 'Helvetica', sans-serif;
  * {
    box-sizing: border-box;
  }

  button {
    cursor: pointer;
  }
}
</style>
