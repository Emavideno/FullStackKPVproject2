<template>
  <div class="about-page">
    <h2>О проекте</h2>
    <p>SPA приложение для создания уютной звуковой атмосферы.</p>
    <div class="guide">
      <h3>Технические особенности:</h3>
      <ul>
        <li>Использование <strong>Vue Router</strong> для навигации.</li>
        <li>Реактивное управление звуком через <strong>YouTube IFrame API</strong>.</li>
        <li>Компонентный подход: ползунки вынесены в отдельный файл.</li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.about-page { max-width: 700px; }

.guide { 
  background: #1e1e1e; 
  padding: 25px; 
  border-radius: 12px; 
  border: 1px solid #333; 
  margin-top: 20px; 
}

h2 { margin-top: 0; }

h3 { color: #646cff; font-size: 1.1rem; }

ul { 
  padding-left: 18px; 
  margin: 0;
}

li { 
  color: #aaa; 
  margin-bottom: 8px; 
  font-size: 0.95rem;
}
</style>