<template>
  <div class="layout">
    <header>
      <h1>Генератор расслабляющей музыки</h1>
      <nav>
        <router-link to="/">Микшер</router-link>
        <router-link to="/about">О проекте</router-link>
      </nav>
    </header>

    <main>
      <router-view />
    </main>

    <footer>
      <p>Генератор расслабляющей музыки — Проект на Vue</p>
    </footer>
  </div>
</template>

<style>
body { 
  background: #121212; 
  color: #eeeeee; 
  margin: 0; 
  font-family: sans-serif; /* Обычный системный шрифт */
}

.layout { 
  max-width: 1000px; 
  margin: 0 auto; 
  padding: 0 20px; 
}

header { 
  display: flex; 
  justify-content: space-between; 
  align-items: center; 
  padding: 20px 0; 
  border-bottom: 1px solid #333; 
}

h1 { 
  color: #646cff; 
  font-size: 1.4rem; 
  margin: 0; 
}

nav a { 
  color: #888; 
  text-decoration: none; 
  margin-left: 20px; 
  font-size: 0.9rem;
}

nav a.router-link-active { 
  color: #646cff; 
  font-weight: bold;
}

main { padding: 30px 0; }

footer { 
  text-align: center; 
  color: #444; 
  padding: 30px 0; 
  font-size: 0.8rem; 
  border-top: 1px solid #222;
}
</style>