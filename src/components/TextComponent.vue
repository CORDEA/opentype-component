<template>
  <canvas ref="el"></canvas>
</template>

<script setup lang="ts">
import { Font, load } from "opentype.js";
import { onMounted, ref } from "vue";

const props = defineProps<{
  text: string;
  fontSize: string;
}>();

const el = ref();
onMounted(function () {
  load("font.ttf", function (err: any, font?: Font) {
    if (err) {
      return;
    }
    if (!font) {
      return;
    }
  });
});
</script>

<style scoped></style>
