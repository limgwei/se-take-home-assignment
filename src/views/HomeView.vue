
<script setup>

import { ref, watch } from 'vue';
import Robot from '../components/Robot.vue';
import Order from '../components/Order.vue';

const robotList = ref([]);
const pendingOrderList = ref([]);
const completeOrderList = ref([]);
const processingTime = ref(10);
let robotNumber = 0;
let orderNumber = 0;
const addRobot = () => {
  robotNumber++;
  robotList.value.push({
    robotNumber: robotNumber,
    currentHandling: null,
    timer: null,
  });
};

const minusBot = () => {
  const robotHandleOrder = robotList.value[robotList.value.length - 1].currentHandling;
  if (robotHandleOrder) {
    let orderIndex = pendingOrderList.value.findIndex((order) => order.orderNumber == robotHandleOrder.orderNumber);
    pendingOrderList.value[orderIndex].status = 0;
    pendingOrderList.value[orderIndex].count = 0;
  }
  clearInterval(robotList.value[robotList.value.length - 1].timer);
  robotList.value.pop();
}

const addOrder = (isVip) => {
  orderNumber += 1;
  const order = {
    orderNumber: orderNumber,
    isVip: isVip,
    status: 0,
    count: 0
  }
  if (isVip) {
    let index = pendingOrderList.value.filter((order) => order.isVip).length - 1;
    pendingOrderList.value.splice(index + 1, 0, order);
  } else {
    pendingOrderList.value.push(order);
  }
}

watch(pendingOrderList, (newValue, oldValue) => {
  assignOrderToRobot();
}, { deep: true });

watch(robotList, (newValue, oldValue) => {
  assignOrderToRobot();
}, { deep: true });

const assignOrderToRobot = () => {
  const robotIdleList = robotList.value.filter((robot) => robot.currentHandling == null);
  const orderIdleList = pendingOrderList.value.filter((order) => order.status == 0);

  if (robotIdleList.length > 0 && orderIdleList.length > 0) {
    const robotIndex = robotList.value.map((robot) => robot.robotNumber).indexOf(robotIdleList[0].robotNumber);
    const orderIndex = pendingOrderList.value.map((order) => order.orderNumber).indexOf(orderIdleList[0].orderNumber);
    robotList.value[robotIndex].currentHandling = pendingOrderList.value[orderIndex];
    pendingOrderList.value[orderIndex].status = 1;
    robotList.value[robotIndex].timer = setInterval(handleUpdateOrderToComplete, 1000, robotIndex, pendingOrderList.value[orderIndex].orderNumber);
  }
}

const handleUpdateOrderToComplete = (robotIndex, orderNumber) => {

  pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count +=1;


  if (pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count >= processingTime.value) {
    pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count = 0;
    completeOrderList.value.push(pendingOrderList.value.find((order) => order.orderNumber == orderNumber));
    const removeIndex = pendingOrderList.value.findIndex((order) => order.orderNumber == orderNumber);
    pendingOrderList.value.splice(removeIndex, 1);
    robotList.value[robotIndex].currentHandling = null;
    clearInterval(robotList.value[robotIndex].timer);
  }
}
</script>

<template >
  <main style="width:100%;height:100%">
    <div style="display: flex;justify-content: flex-end;padding: 2%;gap: 10px;">
      <button @click="addOrder(false)" >New Normal Order</button>
      <button @click="addOrder(true)">New VIP Order</button>
      <button @click="addRobot()">+ Robot</button>
      <button @click="minusBot()">- Bot</button>
    </div>


    <div style="display: flex;justify-content: space-around;">
      <div class="component">
        <h3>Pending Order</h3>
        <div class="component-item">
          <Order v-for="order in pendingOrderList" :order="order" :processingTime ="processingTime" />
        </div>

      </div>

      <div class="component">
        <h3>Processing</h3>
        <div class="component-item">
          <Robot v-for="robot in robotList" :robot="robot" />
        </div>
      </div>

      <div class="component">
        <h3>Complete Order</h3>
        <div class="component-item">
          <Order v-for="order in completeOrderList" :order="order" />
        </div>
      </div>
    </div>

  </main>
</template>

<style scoped>
.vip {
  color: red;
}

.component {
  width: 30%;
  height: 85vh;
  border: 1px solid black;
  text-align: center;
}

.component-item {
  display: flex;
  flex-direction: column;
  gap: 10px;
  padding: 2%;
}
</style>
