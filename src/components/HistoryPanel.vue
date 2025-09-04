<template>
  <div class="history">
    <div class="history-header">
      <span>Historique</span>
      <button class="clear" @click="$emit('clear')">Effacer</button>
    </div>

    <ul class="list" v-if="items.length">
      <li v-for="i in items" :key="i.id" @click="$emit('use', i)">
        <div class="expr">{{ i.expression }}</div>
        <div class="date">{{ formatDate(i.at) }}</div>
      </li>
    </ul>
    <div v-else class="empty">Aucun calcul pour le moment.</div>
  </div>
</template>

<script>
export default {
  props: { items: { type: Array, default: () => [] } },
  methods: {
    formatDate(iso) {
      try {
        const d = new Date(iso);
        return d.toLocaleString();
      } catch { return ""; }
    }
  }
};
</script>

<style scoped>
.history {
  margin-top: 16px;
  background: rgba(255,255,255,0.06);
  border-radius: 14px;
  padding: 10px;
  color: #eee;
}
.history-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-weight: 600;
  margin-bottom: 8px;
}
.clear {
  border: none;
  border-radius: 10px;
  padding: 6px 10px;
  cursor: pointer;
  background: #505050;
  color: #fff;
}
.clear:hover { background: #5a5a5a; }

.list {
  list-style: none;
  margin: 0; padding: 0;
  max-height: 180px; overflow: auto;
}
.list li {
  padding: 8px 10px;
  border-radius: 10px;
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 10px;
  align-items: center;
  cursor: pointer;
}
.list li:hover { background: rgba(255,255,255,0.08); }
.expr { font-size: 14px; }
.date { font-size: 12px; color: #bbb; }
.empty { font-size: 13px; color: #bbb; padding: 8px; }
</style>
