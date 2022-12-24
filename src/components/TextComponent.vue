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
    const canvas = el.value;
    const units = size / font.unitsPerEm;
    const glyphs = [];
    let width = 0;
    for (const t of props.text) {
      const g = f.charToGlyph(t);
      if (!g.advanceWidth) {
        continue;
      }
      width += g.advanceWidth * units;
      glyphs.push(g);
    }

    const maxHeight = glyphs
      .reduce((prev, current) =>
        prev.getMetrics().yMax > current.getMetrics().yMax ? prev : current
      )
      .getMetrics().yMax;
    if (!maxHeight) {
      return;
    }
    canvas.height = maxHeight * units;
    canvas.width = width;

    let x = 0;
    const ctx = canvas.getContext("2d");
    for (const g of glyphs) {
      g.draw(ctx, x, maxHeight * units, size);
      x += g.advanceWidth! * units;
    }
  });
});
</script>

<style scoped></style>
