<template>
  <div class="app">
    <Calculator />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";
export default { components: { Calculator } };
</script>

<style>
/* Fond et centrage global */
html, body, #app { height: 100%; }
body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  background: linear-gradient(135deg, #e5e5e5, #f0f0f0);
}
.app {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
