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
    const units = size / font.unitsPerEm;
    const glyphs = [];
    for (const t of props.text) {
      const g = f.charToGlyph(t);
      if (!g.advanceWidth) {
        continue;
      }
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

    let x = 0;
    for (const g of glyphs) {
      const width = g.advanceWidth! * units;
      g.draw(ctx, x, maxHeight * units, size);
      x += width;
    }
  });
});
</script>

<style scoped></style>
