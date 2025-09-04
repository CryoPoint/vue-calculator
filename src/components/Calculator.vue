<template>
  <div class="wrapper">
    <div class="calculator">
      <div class="time-icon"></div>

      <!-- Écran -->
      <Display :calculation="calculationText" :result="displayValue" />

      <!-- Boutons -->
      <ButtonGrid @input="onKey" />

      <!-- Historique -->
      <HistoryPanel
        :items="history"
        @use="useHistory"
        @clear="clearHistory"
      />
    </div>
  </div>
</template>

<script>
import Display from "./Display.vue";
import ButtonGrid from "./ButtonGrid.vue";
import HistoryPanel from "./HistoryPanel.vue";

const LS_KEY = "calc_history_v1";

export default {
  components: { Display, ButtonGrid, HistoryPanel },
  data() {
    return {
      displayValue: "0",
      firstOperand: null,
      operator: null,
      waitingForSecondOperand: false,
      lastExpression: null,
      history: []
    };
  },
  computed: {
    calculationText() {
      if (this.firstOperand !== null && this.operator) {
        return `${this.formatNumber(this.firstOperand)} ${this.operator}`;
      }
      return "";
    }
  },
  created() {
    try {
      const raw = localStorage.getItem(LS_KEY);
      if (raw) this.history = JSON.parse(raw);
    } catch {}
  },
  mounted() {
    window.addEventListener("keydown", this.handleKey);
  },
  unmounted() {
    window.removeEventListener("keydown", this.handleKey);
  },
  methods: {
    // --- Gestion clavier ---
    handleKey(e) {
      const map = {
        "+": "+",
        "-": "−",
        "*": "×",
        "/": "÷",
        "Enter": "=",
        "=": "=",
        "Backspace": "C",
        "c": "C",
        "Escape": "AC", // Escape = All Clear
        ".": "."
      };

      if (!isNaN(+e.key)) {
        e.preventDefault();
        this.onKey(e.key);
      } else if (map[e.key]) {
        e.preventDefault();
        this.onKey(map[e.key]);
      }
    },

    // --- Historique ---
    saveHistory() {
      try { localStorage.setItem(LS_KEY, JSON.stringify(this.history)); } catch {}
    },
    clearHistory() {
      this.history = [];
      this.saveHistory();
    },
    useHistory(item) {
      this.displayValue = String(item.result);
      this.firstOperand = null;
      this.operator = null;
      this.waitingForSecondOperand = false;
      this.lastExpression = null;
    },

    // --- Gestion des touches ---
    onKey(value) {
      if (!isNaN(+value)) return this.inputDigit(value);
      switch (value) {
        case ".": return this.inputDot();
        case "+": case "−": case "×": case "÷": return this.handleOperator(value);
        case "=": return this.equals();
        case "C": return this.clearEntry();
        case "AC": return this.allClear();   // All Clear
        case "+/-": return this.toggleSign();
        case "%": return this.handlePercent();
      }
    },

    // --- Logique calculatrice ---
    inputDigit(d) {
      if (this.waitingForSecondOperand) {
        this.displayValue = String(d);
        this.waitingForSecondOperand = false;
      } else {
        this.displayValue = this.displayValue === "0" ? String(d) : this.displayValue + d;
      }
    },
    inputDot() {
      if (this.waitingForSecondOperand) {
        this.displayValue = "0.";
        this.waitingForSecondOperand = false;
        return;
      }
      if (!this.displayValue.includes(".")) this.displayValue += ".";
    },
    clearEntry() {
      this.displayValue = "0";
    },
    allClear() {
      this.displayValue = "0";
      this.firstOperand = null;
      this.operator = null;
      this.waitingForSecondOperand = false;
      this.lastExpression = null;
      // si tu veux aussi vider l'historique : décommente la ligne suivante
      // this.clearHistory();
    },
    toggleSign() {
      if (this.displayValue === "0") return;
      this.displayValue = this.displayValue.startsWith("-")
        ? this.displayValue.slice(1)
        : "-" + this.displayValue;
    },
    handlePercent() {
      const v = parseFloat(this.displayValue);
      if (!isNaN(v)) this.displayValue = String(v / 100);
    },
    handleOperator(nextOperator) {
      const inputValue = parseFloat(this.displayValue);

      if (this.operator && this.waitingForSecondOperand) {
        this.operator = nextOperator;
        return;
      }

      if (this.firstOperand === null) {
        this.firstOperand = inputValue;
      } else if (this.operator) {
        const result = this.compute(this.firstOperand, inputValue, this.operator);
        this.displayValue = String(result);
        this.firstOperand = result;
      }

      this.operator = nextOperator;
      this.waitingForSecondOperand = true;
      this.lastExpression = null;
    },
    equals() {
      const inputValue = parseFloat(this.displayValue);

      if (this.lastExpression && !this.operator) {
        const { b, op } = this.lastExpression;
        const result = this.compute(parseFloat(this.displayValue), b, op);
        const expr = `${this.formatNumber(parseFloat(this.displayValue))} ${op} ${this.formatNumber(b)} = ${this.formatNumber(result)}`;
        this.pushHistory(expr, result);
        this.displayValue = String(result);
        return;
      }

      if (this.operator === null || this.firstOperand === null || isNaN(inputValue)) return;

      const a = this.firstOperand;
      const b = inputValue;
      const op = this.operator;

      const result = this.compute(a, b, op);
      const expr = `${this.formatNumber(a)} ${op} ${this.formatNumber(b)} = ${this.formatNumber(result)}`;
      this.pushHistory(expr, result);

      this.displayValue = String(result);
      this.firstOperand = null;
      this.operator = null;
      this.waitingForSecondOperand = false;
      this.lastExpression = { a, b, op };
    },
    compute(a, b, op) {
      switch (op) {
        case "+": return a + b;
        case "−": return a - b;
        case "×": return a * b;
        case "÷": return b === 0 ? NaN : a / b;
        default: return b;
      }
    },
    pushHistory(expression, result) {
      const item = {
        id: Date.now(),
        expression,
        result: Number(result),
        at: new Date().toISOString()
      };
      this.history.unshift(item);
      if (this.history.length > 50) this.history.pop();
      this.saveHistory();
    },
    formatNumber(n) {
      if (!isFinite(n)) return "Erreur";
      const num = Number.parseFloat(n.toString()).toFixed(10).replace(/\.?0+$/, "");
      const parts = num.split(".");
      parts[0] = parts[0].replace(/\B(?=(\d{3})+(?!\d))/g, " ");
      return parts.join(",");
    }
  }
};
</script>

<style scoped>
.wrapper { display: flex; justify-content: center; }
.calculator {
  width: 300px;
  background: #2d2d2d;
  border-radius: 25px;
  padding: 20px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.3),
              0 10px 20px rgba(0,0,0,0.2),
              inset 0 1px 0 rgba(255,255,255,0.1);
  position: relative;
  animation: fadeInUp 0.6s ease;
}
.time-icon {
  position: absolute;
  top: 15px;
  left: 20px;
  width: 35px;
  height: 35px;
  background: rgba(255,255,255,0.15);
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.time-icon::after { content: '⏱'; color: #888; font-size: 16px; }
@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}
</style>
