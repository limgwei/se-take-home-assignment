
<script setup>

import { ref, watch } from 'vue';
import Robot from '../components/Robot.vue';
import Order from '../components/Order.vue';
import ButtonControl from '../components/ButtonControl.vue';

const robotList = ref([]);
const pendingOrderList = ref([]);
const completeOrderList = ref([]);
const processingTime = ref(10);
let robotNumber = 0;
let orderNumber = 0;
const addRobot = () => {

  robotNumber++;
  robotList.value.push({
    robotNumber: robotNumber,   //unique number
    currentHandling: null, //use for handle
    timer: null, //timer to control set Interval
  });
};

const minusBot = () => {

  if (robotList.value.length == 0) {
    return;
  }

  if (robotList.value[robotList.value.length - 1].currentHandling != null) {
    //get the latest robot
    const robotHandleOrder = robotList.value[robotList.value.length - 1].currentHandling;

    //get queue order
    const orderIndex = pendingOrderList.value.findIndex((order) => order.orderNumber == robotHandleOrder.orderNumber);
    //set its handling order to pending
    pendingOrderList.value[orderIndex].status = 0;
    pendingOrderList.value[orderIndex].count = 0;

    //destroy the robot
    clearInterval(robotList.value[robotList.value.length - 1].timer);
  }

  robotList.value.pop();
}

const addOrder = (isVip) => {
  orderNumber += 1;
  const order = {
    orderNumber: orderNumber, //unique number
    isVip: isVip, //check is VIP
    status: 0, //0 = idle, 1 = processing , 2 = completed(to show in complete array)
    count: 0, //get count
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

  // check if current idle robot and idle order
  if (robotIdleList.length > 0 && orderIdleList.length > 0) {

    // get the robot (ascending)
    const robotIndex = robotList.value.map((robot) => robot.robotNumber).indexOf(robotIdleList[0].robotNumber);
    // get the order (ascending)
    const orderIndex = pendingOrderList.value.map((order) => order.orderNumber).indexOf(orderIdleList[0].orderNumber);
    //set order to robot, change order status to processing
    robotList.value[robotIndex].currentHandling = pendingOrderList.value[orderIndex];
    pendingOrderList.value[orderIndex].status = 1;
    //start the count
    robotList.value[robotIndex].timer = setInterval(handleUpdateOrderToComplete, 1000, robotIndex, pendingOrderList.value[orderIndex].orderNumber);
  }
}

const handleUpdateOrderToComplete = (robotIndex, orderNumber) => {
  pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count += 1;

  //if equal 10
  if (pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count >= processingTime.value) {
    // set count to 0(optional), status = 2 (to ensure the time wont show in complate order),
    pendingOrderList.value.find((order) => order.orderNumber == orderNumber).count = 0;
    pendingOrderList.value.find((order) => order.orderNumber == orderNumber).status = 2;
    //add to completeOrderList
    completeOrderList.value.push(pendingOrderList.value.find((order) => order.orderNumber == orderNumber));
    // remove from pendingOrderList
    const removeIndex = pendingOrderList.value.findIndex((order) => order.orderNumber == orderNumber);
    pendingOrderList.value.splice(removeIndex, 1);
    //stop the robot
    robotList.value[robotIndex].currentHandling = null;
    clearInterval(robotList.value[robotIndex].timer);
  }
}
</script>

<template >
  <main>
    <div style="display: flex;justify-content: flex-end;padding: 2%;gap: 10px;">
      <ButtonControl @button-handler="addOrder(false)" text="New Normal Order" classElement="normal-order" />
      <ButtonControl @button-handler="addOrder(true)" text="New VIP Order" classElement="vip-order" />
      <ButtonControl @button-handler="addRobot()" text="+ Robot" classElement="add-robot" />
      <ButtonControl @button-handler="minusBot()" text="- Robot" classElement="minus-robot" />
    </div>


    <div style="display: flex;justify-content: space-around;">
      <div class="component">
        <h3>Pending Order</h3>
        <div class="component-item">
          <Order v-for="order in pendingOrderList" :order="order" :processingTime="processingTime" />
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
