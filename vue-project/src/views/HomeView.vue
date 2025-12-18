<template>
  <div class="mixer-page">
    <div class="master-box">
      <h3>Общая громкость</h3>
      
      <p v-if="activeSoundsCount > 0" class="status-text">
        Активных источников: {{ activeSoundsCount }}
      </p>
      <p v-else class="status-text">Все звуки выключены</p>

      <input type="range" min="0" max="100" v-model="masterVolume">
      <div class="master-val">{{ masterVolume }}%</div>
    </div>

    <div class="sounds-grid">
      <SoundSlider 
        v-for="sound in sounds" 
        :key="sound.id"
        :label="sound.name"
        :icon="sound.icon"
        v-model:volume="sound.volume"
      />
    </div>

    <div class="custom-link-section">
      <h3>Своя ссылка с YouTube</h3>
      <div class="custom-link-group">
        <input 
          v-model="customUrl" 
          placeholder="https://youtu.be/..." 
          @change="handleCustomUrl"
          class="url-input-field"
        >
        <div class="custom-slider-container">
          <SoundSlider 
            label="Громкость ссылки" 
            icon="🔗" 
            v-model:volume="customVolume" 
          />
        </div>
      </div>
    </div>

    <div id="yt-api-containers" style="display: none">
      <div v-for="s in sounds" :key="s.id" :id="'player-' + s.id"></div>
      <div id="player-custom"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, watch, onMounted, onActivated, computed } from 'vue';
import SoundSlider from '../components/SoundSlider.vue';

const masterVolume = ref(50);
const customUrl = ref('');
const customVolume = ref(0);
const players = {}; 

const sounds = reactive([
  { id: 'rain', name: 'Дождь', icon: '🌧️', url: 'https://youtu.be/mPZkdNFkNps', volume: 0 },
  { id: 'cafe', name: 'Кафе', icon: '☕', url: 'https://youtu.be/h2zkV-l_TbY', volume: 0 },
  { id: 'delta', name: 'Дельта волны', icon: '💤', url: 'https://youtu.be/go99WqXWGgk', volume: 0 },
  { id: 'fire', name: 'Камин', icon: '🔥', url: 'https://youtu.be/L_LUpnjgPso', volume: 0 },
  { id: 'lo-fi', name: 'Lo-Fi', icon: '🎧', url: 'https://youtu.be/5jaT_8hy3Vg?si=05UKFUC2s1PgPR7A', volume: 0 },
  { id: 'forest', name: 'Лес', icon: '🌲', url: 'https://youtu.be/xNN7iTA57jM?si=ubc7NJJhFkzjZTuX', volume: 0 }
]);

// COMPUTED: Вычисляем количество активных источников звука
const activeSoundsCount = computed(() => {
  const standardActive = sounds.filter(s => s.volume > 0).length;
  const customActive = customVolume.value > 0 ? 1 : 0;
  return standardActive + customActive;
});

const getIdFromUrl = (url) => {
  if (!url) return null;
  const parts = url.split('/');
  const lastPart = parts[parts.length - 1];
  return lastPart ? lastPart.split('?')[0] : null;
};

function createPlayer(id, videoUrl, currentVolume) {
  const vId = getIdFromUrl(videoUrl);
  if (!vId) return;

  players[id] = new YT.Player(`player-${id}`, {
    videoId: vId,
    playerVars: { 
      loop: 1, 
      playlist: vId, 
      autoplay: 1, 
      origin: window.location.origin,
      controls: 0
    },
    events: {
      onReady: (event) => {
        event.target.unMute();
        applyVolume(event.target, currentVolume);
      }
    }
  });
}

function applyVolume(player, indVol) {
  if (player && typeof player.setVolume === 'function') {
    const finalVol = indVol * (masterVolume.value / 100);
    player.setVolume(finalVol);
    if (finalVol > 0) {
      player.playVideo();
    } else {
      player.pauseVideo();
    }
  }
}

watch([sounds, masterVolume, customVolume], () => {
  sounds.forEach(s => {
    if (s.volume > 0) {
      if (!players[s.id]) {
        createPlayer(s.id, s.url, s.volume);
      } else {
        applyVolume(players[s.id], s.volume);
      }
    } else if (players[s.id]) {
      applyVolume(players[s.id], 0);
    }
  });

  if (players['custom']) {
    applyVolume(players['custom'], customVolume.value);
  }
}, { deep: true });

onMounted(() => {
  if (!window.YT) {
    const tag = document.createElement('script');
    tag.src = "https://www.youtube.com/iframe_api";
    document.head.appendChild(tag);
  }
});

onActivated(() => {
  sounds.forEach(s => {
    if (players[s.id]) applyVolume(players[s.id], s.volume);
  });
});

function handleCustomUrl() {
  const id = getIdFromUrl(customUrl.value);
  if (id && id.length === 11) {
    if (players['custom'] && typeof players['custom'].loadVideoById === 'function') {
      players['custom'].loadVideoById(id);
    } else {
      createPlayer('custom', customUrl.value, customVolume.value);
    }
  }
}
</script>

<style scoped>
.mixer-page {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.master-box {
  background: #1e1e1e;
  padding: 20px;
  border-radius: 12px;
  text-align: center;
  border: 1px solid #333;
}

.status-text {
  color: #888;
  font-size: 0.9rem;
  margin-bottom: 10px;
}

.master-val {
  font-size: 1.8rem;
  color: #646cff;
  font-weight: bold;
  margin-top: 5px;
}

.sounds-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 20px;
}

.custom-link-section {
  margin-top: 10px;
  padding: 20px;
  background: #1e1e1e;
  border-radius: 12px;
  border: 1px solid #333;
}

.custom-link-group {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.url-input-field {
  width: 100%;
  padding: 12px 20px;
  background: #121212;
  border: 1px solid #333;
  color: white;
  border-radius: 25px;
  outline: none;
  font-size: 0.9rem;
  box-sizing: border-box;
}

.url-input-field:focus {
  border-color: #646cff;
}

@media (min-width: 850px) {
  .custom-link-group {
    flex-direction: row;
    align-items: center;
  }
  .url-input-field {
    flex: 1;
  }
  .custom-slider-container {
    min-width: 380px;
  }
}
</style>