<template>
  <div id="app">
    <OrderDashboard />
    <OrderFilters />
  </div>
</template>

<script>
import OrderDashboard from "./components/OrderDashboard.vue";
import OrderFilters from "./components/OrderFilters.vue";

export default {
  components: {
    OrderDashboard,
    OrderFilters,
  },
};
</script>
