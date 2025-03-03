<template>
  <Header />
  <Column />

</template>
<script>
import Header from './components/header/header.vue'
import Column from './components/column/column.vue'
export default {
  name: "App",
  components: {
    Header,
    Column
  }
};

</script>