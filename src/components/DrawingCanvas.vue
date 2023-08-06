<script setup lang="ts">
import { reactive, ref } from 'vue';
import PalettePanel from './PalettePanel.vue';

type LayerType = {
  visible: boolean;
  name: string;
  content: Array<number[]>
}

function buildLayer(cols: number, rows: number, name: string): LayerType {
  return { visible: true, name, content: [new Array(cols * rows).fill(0),] };
}

const PIXEL_SIZE_INIT = 16;
const PIXEL_COLOR_INDEX_INIT = 0;
const LAYER_COLUMNS_INIT = 32;
const LAYER_ROWS_INIT = 32;
const PALETTE_INIT = ["rgba(0, 0, 0, 0)", "#ff0000", "#00ff00", "0000ff", "#ffff00", "#00ffff", "#ff00ff", "#000000", "#ffffff"];

const pixelSize = ref(PIXEL_SIZE_INIT);
const palette = ref(PALETTE_INIT);
const selectedPaletteIndex = ref(1);
const selectedLayerIndex = ref(0);
const selectedFrameIndex = ref(0);
const dimensions = reactive({ columns: LAYER_COLUMNS_INIT, rows: LAYER_ROWS_INIT });
const layers = reactive(
  [
    buildLayer(dimensions.columns, dimensions.rows, `layer-0`),
  ]);

function addLayer() {
  layers.push(buildLayer(dimensions.columns, dimensions.rows, `layer-${layers.length}`));
}

function deleteLayer(layerIndex: number) {
  if (layers.length == 1) {
    const blankLayer = buildLayer(dimensions.columns, dimensions.rows, 'layer-0');
    layers.splice(layerIndex, 1, blankLayer);
    return;
  }
  layers.splice(layerIndex, 1);
}
const mouseDown = ref(false);

function tryPaintPixel(pixelIndex: number) {
  // TODO: check the active layer
  if (!mouseDown.value) return;
  // TODO: check if pencil is active tool
  layers[selectedLayerIndex.value].content[selectedFrameIndex.value][pixelIndex] = selectedPaletteIndex.value;
}

function changeSelectedPaletteColor(newColorIndex: number){
  selectedPaletteIndex.value=newColorIndex;
}
</script>

<template>
  <PalettePanel :palette="palette" :selectedPaletteIndex="selectedPaletteIndex" @change-selected-palette-color="changeSelectedPaletteColor"/>
  <section class="central-area">
    <section class="layers" @mousedown.prevent="mouseDown = true;" @mouseup.prevent="mouseDown = false;" @mouseleave.prevent="mouseDown=false;"
      :style="{ width: (pixelSize * dimensions.columns) + 'px', height: (pixelSize * dimensions.rows) + 'px' }">
      <div v-for="layer in layers" class="layer" :style="{ width: (pixelSize * dimensions.columns) + 'px' }"
        :class="{ 'layer-hidden': !layer.visible, }">
        <div v-for="(pixel, pIndex) in layer.content[selectedFrameIndex]" @mouseover="() => tryPaintPixel(pIndex)"
          :style="{ background: palette[pixel], outline: '1px solid orange', width: pixelSize + 'px', height: pixelSize + 'px' }">
        </div>
      </div>
    </section>
    <section class="layers-details">
      <div v-for="layer in layers" class="layers-details__actions">
        <button>
          <span v-if="layer.visible">👁️</span>
          <span v-else>🚫</span>
        </button>
        <button>
          ❌
        </button>
        <button>
          R
        </button>
        <span>{{ layer.name }}</span>
      </div>
      <button @click="addLayer">+ New layer</button>
    </section>
  </section>
</template>

<style scoped>
.central-area {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  height: 100vh;
  gap: 1rem;
}

.layers {
  position: relative;

}

.layer {
  position: absolute;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.layer-hidden {
  display: none;
}


.layer-details {
  display: flex;
  flex-direction: columns;
  position: relative;
}

.layer-details__actions {
  display: flex;
  flex-direction: row;
}
</style>
