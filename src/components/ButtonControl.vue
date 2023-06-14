<script setup>
import {defineEmits } from 'vue';

const emit = defineEmits(["button-handler"]);

const props = defineProps({
  text:{
    type: String,
    required:true,
  },
  classElement:{
    type:String,
    required:true
  }
});

const buttonHandler = () =>{
  emit("button-handler");
  return ;
};
</script>

<template>
  <div>
    <button @click="buttonHandler()" :class="classElement"> {{ text }} </button>
  </div>
</template>


<style scoped>

button{

  border: none;
  color: white;
  padding: 7.5px 16px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  border-radius: 5%;
}
  .normal-order{
    background-color: #4CAF50;
  }

  .vip-order{
    background-color: #008CBA;
  }

  .add-robot{
    background-color: #e7e7e7; color: black;
  }

  .minus-robot{
    background-color: #f44336;
  }
</style>
