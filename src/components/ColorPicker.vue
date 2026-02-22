<script setup lang="ts">
import { ref, useTemplateRef, nextTick } from "vue";

const imageUrl = ref<string | null>(null);
const pickedColor = ref<string>("#ffffff");
const canvasRef = useTemplateRef<HTMLCanvasElement>("canvasRef");

// 画像読み込み処理
const handleFileUpload = async (event: Event) => {
  const file = (event.target as HTMLInputElement).files?.[0];
  if (!file) return;

  imageUrl.value = URL.createObjectURL(file);
  await nextTick();

  const img = new Image();
  img.onload = () => {
    const canvas = canvasRef.value;
    if (!canvas) return;
    const ctx = canvas.getContext("2d", { willReadFrequently: true });
    canvas.width = img.width;
    canvas.height = img.height;
    ctx?.drawImage(img, 0, 0);
  };
  img.src = imageUrl.value;
};

// カラーピック処理（表示サイズと実サイズの比率を計算）
const pickColor = (event: MouseEvent) => {
  const canvas = canvasRef.value;
  if (!canvas) return;
  const ctx = canvas.getContext("2d");
  const rect = canvas.getBoundingClientRect();
  const x = (event.clientX - rect.left) * (canvas.width / rect.width);
  const y = (event.clientY - rect.top) * (canvas.height / rect.height);

  const pixel = ctx?.getImageData(x, y, 1, 1).data;
  if (pixel) {
    const hex =
      "#" +
      Array.from(pixel.slice(0, 3))
        .map((x) => x.toString(16).padStart(2, "0"))
        .join("");
    pickedColor.value = hex;
    navigator.clipboard.writeText(hex);
  }
};
</script>

<template>
  <main class="app-container">
    <header>
      <p>アップロードした画像のHTMLカラーコードを取得します。</p>
      <div class="controls">
        <label class="file-label">
          画像を選択
          <input
            type="file"
            @change="handleFileUpload"
            accept="image/*"
            hidden
          />
        </label>

        <div class="result-badge" :style="{ backgroundColor: pickedColor }">
          <code>{{ pickedColor }}</code>
          <small>Copied!</small>
        </div>
      </div>
    </header>

    <div v-if="imageUrl" class="canvas-wrapper">
      <canvas ref="canvasRef" @click="pickColor"></canvas>
    </div>

    <div v-else class="drop-placeholder">画像をアップロードしてください</div>
  </main>
</template>

<style scoped>
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  overflow: hidden;
}

* {
  box-sizing: border-box;
}

.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  padding: 1rem;
  background-color: #f8f9fa;
  gap: 1rem;
}

header {
  flex: 0 0 auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.canvas-wrapper,
.drop-placeholder {
  flex: 1;
  min-height: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #eee;
  border-radius: 8px;
  overflow: hidden;
}

canvas {
  max-width: 100%;
  max-height: 100%;
  display: block;
  cursor: crosshair;
}

.controls {
  display: flex;
  align-items: center;
  gap: 1rem;
}
.file-label {
  background: #333;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
}
.result-badge {
  padding: 0.5rem 1rem;
  border-radius: 4px;
  border: 1px solid #ddd;
  min-width: 120px;
  text-align: center;
}
</style>
