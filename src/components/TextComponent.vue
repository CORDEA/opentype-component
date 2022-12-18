<template>
  <canvas ref="el"></canvas>
</template>

<script setup lang="ts">
import { Font, load, parse } from "opentype.js";
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
    const size = parseInt(props.fontSize);
    const f = parse(font.toArrayBuffer());
    const ctx = el.value.getContext("2d");
    for (const t of props.text) {
      const g = f.charToGlyph(t);
      if (!g.advanceWidth) {
        continue;
      }
      g.draw(ctx, 0, 0, size);
    }
  });
});
</script>

<style scoped></style>
