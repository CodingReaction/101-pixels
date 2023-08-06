<script setup lang="ts">
defineProps({
    palette: Array<string>,
    selectedPaletteIndex: Number
});
const emit = defineEmits(["change-selected-palette-color"]);
</script>

<template>
  <section class="palette">
    <button v-for="(color, cIndex) in palette" :style="{ background: color }" class="palette__color"
      :class="{ 'palette__color-selected': cIndex == selectedPaletteIndex }"
      @click.prevent="() => emit('change-selected-palette-color', cIndex)">
    </button>
  </section>
</template>

<style scoped>
.palette {
  margin: 1rem;
  position: absolute;
  display: flex;
  flex-direction: column;
}

.palette__color {
  width: 64px;
  height: 64px;
}

.palette__color-selected {
  border: 2px dashed orange;
}
</style>
