<script setup>

const props = defineProps({
  robot: {
    type: Object,
    required: true,
  }
});
</script>

<template>
  <div>
    <div v-if="robot.currentHandling" :class="{vip:robot.currentHandling.isVip}" class="text-decoration">Robot {{ robot.robotNumber }} is processing Order {{ robot.currentHandling.orderNumber }}</div>
    <div v-else class="text-decoration">Robot {{ robot.robotNumber }} is Idle</div>
    <hr class="divider">

  </div>
</template>



<style scoped>
.vip{
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
}

</style>
