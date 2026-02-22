<script setup lang="ts">
import ColorPicker from "./components/ColorPicker.vue";
</script>

<template>
  <main class="app-container">
    <br />
    <h1>カラーピックくん</h1>
    <ColorPicker />
  </main>
</template>

<style>
/* グローバルな設定（App.vue 以外に書いてもOK） */
html,
body {
  margin: 0;
  padding: 0;
  height: 100%;
  overflow: hidden;
  font-family: sans-serif;
}

.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  padding: 1rem;
  background-color: #f8f9fa;
}

h1 {
  margin: 0 0 1rem 0;
  font-size: 1.5rem;
  flex-shrink: 0;
}
</style>
