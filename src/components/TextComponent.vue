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

    const maxY = glyphs
      .map((e) => [e.getMetrics().yMax, e.getMetrics().yMin])
      .reduce((prev, current) => [
        prev[0] > current[0] ? prev[0] : current[0],
        prev[1] > current[1] ? current[1] : prev[1],
      ]);
    const height = maxY[0] - maxY[1];
    const baseline = maxY[0] * units;
    canvas.height = height * units;
    canvas.width = width;

    let x = 0;
    const ctx = canvas.getContext("2d");
    for (const g of glyphs) {
      g.draw(ctx, x, baseline, size);
      x += g.advanceWidth! * units;
    }
  });
});
</script>

<style scoped></style>
