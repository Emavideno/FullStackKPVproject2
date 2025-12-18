<template>
  <div class="slider-card" :class="{ active: volume > 0 }">
    <div class="info">
      <span class="icon">{{ icon }}</span>
      <span class="label">{{ label }}</span>
    </div>
    <input 
      type="range" 
      min="0" max="100" 
      :value="volume" 
      @input="$emit('update:volume', Number($event.target.value))"
    >
    <span class="percent">{{ volume }}%</span>
  </div>
</template>

<script setup>
defineProps(['label', 'volume', 'icon']);
defineEmits(['update:volume']);
</script>

<style scoped>
.slider-card { 
  background: #1e1e1e; 
  padding: 20px; 
  border-radius: 12px; 
  display: flex; 
  align-items: center; 
  gap: 15px; 
  border: 1px solid #333; 
}

/* Только рамка меняет цвет, без свечения и движения */
.active { 
  border-color: #646cff; 
}

.info { 
  display: flex; 
  flex-direction: column; 
  min-width: 90px; 
}

.icon { font-size: 1.5rem; }

.label { 
  font-size: 0.8rem; 
  color: #888; 
}

input[type="range"] { 
  flex: 1; 
  accent-color: #646cff; 
  cursor: pointer; 
}

.percent { 
  width: 40px; 
  text-align: right; 
  color: #646cff; 
  font-size: 0.9rem;
}
</style>