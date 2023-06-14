<script setup>
const props = defineProps({
  order: {
    type: Object,
    required: true,
  },
  processingTime:{
    type:Number,
    required:false,
  }
});
</script>

<template>
  <div :class="{ vip: order.isVip}" class="text-decoration">{{ order.orderNumber }}
  <label v-show="order.status==1">Left: {{ processingTime - order.count }}</label>
  </div>
</template>



<style scoped>
.vip {
  color: red;
}
.text-decoration{
  font-size: 20px;
  width: 100%;
  text-align: center;
  border: 1px solid black;
  padding-top: 2%;
  padding-bottom: 2%;
}

</style>
