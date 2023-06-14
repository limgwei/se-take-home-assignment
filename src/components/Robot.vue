<script setup>

const props = defineProps({
  robot: {
    type: Object,
    required: true,
  },
  // processingTime:{
  //   type:Number,
  //   required:true
  // }
});
</script>

<template>
  <div>
    <div v-if="robot.currentHandling" :class="{vip:robot.currentHandling.isVip}" class="text-decoration">Robot {{ robot.robotNumber }} is processing Order {{ robot.currentHandling.orderNumber }} </div>
    <div v-else class="text-decoration">Robot {{ robot.robotNumber }} is Idle</div>
  </div>
</template>



<style scoped>
.vip{
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
