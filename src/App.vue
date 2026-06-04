<template>
  <h1>Average: {{ average }}</h1>
  <h1>Highest: {{ highest }}</h1>
  <h1>Lowest: {{ lowest }}</h1>
  <!-- <h1>isPassing: {{ isPassing }}</h1> -->
  <!-- <h1>isPassing: {{ isPassing }}</h1> -->
</template>

<script setup>
import { ref, computed} from 'vue';
const studentName = ref('Nona');
const scores = ref([
  {'subject':'Math', 'score':78},
  {'subject':'English', 'score':98},
]);
const passMark = ref(50);

const average = computed(()=>{
  let total = 0;
  scores.value.forEach(subject => {
    total += subject.score;
  }); 
  return total / scores.value.length;
})

const highest = computed(()=>{
  let max = scores.value[0].score;
  scores.value.forEach(subject => {
    if (max < subject.score) {
      max = subject.score;
    }
  });
  return max;
})

const lowest = computed(()=>{
  let min = scores.value[1].score;
  scores.value.forEach(subject => {
    if (min > subject.score) {
      min = subject.score;
    }
  });
  return min;
})

const grade = computed(()=>{

})

// const isPassing = computed(()=>{
//   if (average > passMark.value) {
//     return true;
//   }
// })
</script>

<style scoped>
</style>
