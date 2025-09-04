<template>
  <div class="display">
    <div class="calculation">{{ calculation }}</div>
    <div class="result">{{ result }}</div>
  </div>
</template>

<script>
export default {
  props: {
    calculation: { type: String, default: "" },
    result: { type: String, default: "0" }
  }
};
</script>

<style scoped>
.display {
  height: 150px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-end;
  padding: 20px 0;
  margin-bottom: 20px;
}
.calculation {
  color: #888;
  font-size: 18px;
  font-weight: 300;
  margin-bottom: 10px;
  opacity: 0.8;
}
.result {
  color: #fff;
  font-size: 48px;
  font-weight: 200;
  line-height: 1;
}
</style>
