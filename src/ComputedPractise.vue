<template>
  <h1>Student: {{ studentName }}</h1>
  <h1>Average: {{ average }}</h1>
  <h1>Highest: {{ highest }}</h1>
  <h1>Lowest: {{ lowest }}</h1>
  <h1>Grade: {{ grade }}</h1>
  <h1>Passing: {{ isPassing }}</h1>
  <h1>Summary: {{ summary }}</h1>
</template>

<script setup>
import { ref, computed } from 'vue';

const studentName = ref('Nona');

const scores = ref([
  { subject: 'Math', score: 78 },
  { subject: 'English', score: 98 },
]);

const passMark = ref(50);

// Average score
const average = computed(() => {
  let total = 0;

  scores.value.forEach(subject => {
    total += subject.score;
  });

  return total / scores.value.length;
});

// Highest score
const highest = computed(() => {
  let max = scores.value[0].score;

  scores.value.forEach(subject => {
    if (subject.score > max) {
      max = subject.score;
    }
  });

  return max;
});

// Lowest score
const lowest = computed(() => {
  let min = scores.value[0].score;

  scores.value.forEach(subject => {
    if (subject.score < min) {
      min = subject.score;
    }
  });

  return min;
});

// Letter grade
const grade = computed(() => {
  if (average.value >= 90) {
    return 'A';
  } else if (average.value >= 80) {
    return 'B';
  } else if (average.value >= 70) {
    return 'C';
  } else if (average.value >= 60) {
    return 'D';
  } else {
    return 'F';
  }
});

// Passing status
const isPassing = computed(() => {
  return average.value >= passMark.value;
});

// Summary
const summary = computed(() => {
  return `${studentName.value} - Avg: ${average.value.toFixed(1)} - ${
    isPassing.value ? 'PASSING' : 'FAILED'
  }`;
});
</script>

<style scoped>
h1 {
  margin: 5px 0;
}
</style>