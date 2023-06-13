<script setup>
const props = defineProps({
  order: {
    type: Object,
    required: true,
  }
});
</script>

<template>
  <div :class="{ vip: order.isVip}" class="text-decoration">{{ order.orderNumber }}</div>
  <hr class="divider">
</template>



<style scoped>
.vip {
  color: red;
}
.text-decoration{
  font-size: 30px;
  width: 100%;
  text-align: center;
  border: 1px solid black;
}

.divider {
  border-top: 3px solid #bbb;
  width: 100%;
}
</style>
