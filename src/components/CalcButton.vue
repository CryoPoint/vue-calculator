<template>
  <button :class="['button', buttonClass, { 'button-zero': wide }]">
    {{ label }}
  </button>
</template>

<script>
export default {
  props: {
    label: String,
    type: { type: String, default: "gray" },
    wide: { type: Boolean, default: false }
  },
  computed: {
    buttonClass() {
      return this.type === "orange" ? "button-orange" : "button-gray";
    }
  }
};
</script>



<style scoped>
.button {
  height: 65px;
  border-radius: 16px;
  border: none;
  font-size: 24px;
  font-weight: 400;
  cursor: pointer;
  transition: all 0.15s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
  animation: fadeInUp 0.6s ease;
}
.button:hover { transform: scale(1.05); }
.button:active { transform: scale(0.95); }

/* styles fidèles à ta maquette (gris/orange, ombres) */
.button-gray {
  background: #505050;
  color: white;
  box-shadow: 0 4px 8px rgba(0,0,0,0.3),
              inset 0 1px 0 rgba(255,255,255,0.1);
}
.button-gray:hover { background: #5a5a5a; }
.button-orange {
  background: linear-gradient(135deg, #ff9500, #ff8c00);
  color: white;
  box-shadow: 0 4px 8px rgba(255,149,0,0.3),
              inset 0 1px 0 rgba(255,255,255,0.2);
}
.button-orange:hover { background: linear-gradient(135deg, #ffab33, #ff9500); }
.button-zero {
  grid-column: span 2;
  justify-content: flex-start;
  padding-left: 25px;
}
.button::before {
  content: '';
  position: absolute; top: 0; left: -100%; width: 100%; height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
  transition: left 0.5s;
}
.button:hover::before { left: 100%; }

/* même animation que le conteneur */ /* source: ton fichier */
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}
</style>
