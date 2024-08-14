<script setup lang="ts">
import WaveSurfer from "wavesurfer.js";
import Spectrogram from "wavesurfer.js/dist/plugins/spectrogram";

import { onMounted } from "vue";

let wavesurfer: WaveSurfer;
let spectrogram: Spectrogram;
let minPixPerSec: number;

function registWaveSurferEvents() {
  wavesurfer.on("ready", () => {
    const width = wavesurfer.getWidth();
    const duration = wavesurfer.getDuration();
    minPixPerSec = width / duration;
    wavesurfer.options.minPxPerSec = minPixPerSec;
    wavesurfer.options.barHeight = 1;
  });
  wavesurfer.on("click", () => wavesurfer.play());
}
function registWaveSurferPlugins() {
  spectrogram = Spectrogram.create({
    labels: true,
    scale: "linear",
  });
  wavesurfer.registerPlugin(spectrogram);
}
function zoom(scale: number) {
  const oldPixPerSec = wavesurfer.options.minPxPerSec;
  const newPixPerSec = oldPixPerSec * scale;
  if (newPixPerSec < minPixPerSec) return;
  wavesurfer.setOptions({ minPxPerSec: newPixPerSec });
}
function stretch(offset: number) {
  const oldBarHeight = wavesurfer.options.barHeight ?? 1;
  const newBarHeight = oldBarHeight + offset;
  if (newBarHeight < 1) return;
  wavesurfer.setOptions({ barHeight: newBarHeight });
}

onMounted(() => {
  wavesurfer = WaveSurfer.create({
    container: "#wavesurfer",
    progressColor: "#4f4fd9",
    waveColor: "#1b1bb3",
    url: "/example.wav",
  });
  registWaveSurferEvents();
  registWaveSurferPlugins();
});
</script>

<template>
  <div>
    <div id="wavesurfer"></div>
    <button @click="wavesurfer.playPause">toggle</button>
    <button @click="zoom(2)">zoom in</button>
    <button @click="zoom(0.5)">zoom out</button>
    <button @click="stretch(0.1)">expand</button>
    <button @click="stretch(-0.1)">shrink</button>
  </div>
</template>

<style scoped></style>
