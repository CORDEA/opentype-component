<template>
  <canvas ref="el"></canvas>
</template>

<script setup lang="ts">
const props = defineProps<{
  text: string;
  fontSize: string;
}>();
</script>

<style scoped></style>
