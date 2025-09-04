<template>
  <div class="buttons">
    <CalcButton v-for="(btn, i) in buttons"
      :key="i"
      :label="btn.label"
      :type="btn.type"
      :wide="btn.wide"
      @click="$emit('input', btn.label)" />
  </div>
</template>

<script>
import CalcButton from "./CalcButton.vue";

export default {
  components: { CalcButton },
  data() {
    return {
      buttons: [
        { label: "AC", type: "gray" },  // 🆕 All Clear
        { label: "C", type: "gray" },   // 🆕 Clear Entry
        { label: "%", type: "gray" },
        { label: "÷", type: "orange" },

        { label: "7", type: "gray" }, { label: "8", type: "gray" }, { label: "9", type: "gray" }, { label: "×", type: "orange" },
        { label: "4", type: "gray" }, { label: "5", type: "gray" }, { label: "6", type: "gray" }, { label: "−", type: "orange" },
        { label: "1", type: "gray" }, { label: "2", type: "gray" }, { label: "3", type: "gray" }, { label: "+", type: "orange" },
        { label: "0", type: "gray", wide: true }, { label: ".", type: "gray" }, { label: "=", type: "orange" }
      ]
    };
  }
};
</script>

<style scoped>
.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}
</style>
